getgenv().autoSellEnabled = true;

if not getgenv().autoSellEnabled then
    return;
end

local getPlayerData = game:GetService("ReplicatedStorage"):FindFirstChild("GameNetwork"):FindFirstChild("ServerNetwork"):FindFirstChild("GetPlayerData");
local sellClothing = game:GetService("ReplicatedStorage"):FindFirstChild("GameNetwork"):FindFirstChild("ServerNetwork"):FindFirstChild("SellClothing");

local itemsSold = 0;
local sellDelay = 8;

coroutine.resume(coroutine.create(function()
    while task.wait(sellDelay) do
        if not getgenv().autoSellEnabled then
            coroutine.yield();
        end

        if itemsSold >= 10 then
            task.wait(5);
            itemsSold = 0;
        end

        local playerData = getPlayerData:InvokeServer();

        for name,data in pairs(playerData) do
            if name ~= "Inventory" or type(data) ~= "table" then 
                continue 
            end

            for _,itemData in pairs(data) do
                local itemName = itemData["ItemName"];
                local itemHash = itemData["ItemHash"];
                local itemPrice = itemData["Price"];

                if itemName == nil or itemHash == nil or itemPrice == nil then
                    continue;
                end

                sellClothing:InvokeServer(itemHash);
                itemsSold += 1;

                print("[Автопродажа | Предмет] Название: "..tostring(itemName)..", Хэш: "..tostring(itemHash)..", Цена: "..tostring(itemPrice));
            end
        end
    end
end))
