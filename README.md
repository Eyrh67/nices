-- Load Kavo UI Library
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("ESP Menu", "DarkTheme") -- Tạo cửa sổ ESP Menu

-- Tạo một Tab
local Tab = Window:NewTab("Main")

-- Tạo Section trong Tab
local Section = Tab:NewSection("ESP")

-- ESP function
local function toggleESP(state)
    if state then
        -- Nếu bật ESP
        for _, player in pairs(game.Players:GetPlayers()) do
            if player ~= game.Players.LocalPlayer then
                if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                    local highlight = Instance.new("Highlight")
                    highlight.Parent = player.Character
                    highlight.FillColor = Color3.fromRGB(0, 255, 0) -- Màu xanh lá cho ESP
                    highlight.OutlineTransparency = 0
                    highlight.FillTransparency = 0.7
                    highlight.Name = "ESPHighlight"
                end
            end
        end
    else
        -- Nếu tắt ESP
        for _, player in pairs(game.Players:GetPlayers()) do
            if player.Character and player.Character:FindFirstChild("ESPHighlight") then
                player.Character:FindFirstChild("ESPHighlight"):Destroy()
            end
        end
    end
end

-- Thêm nút bật/tắt ESP
Section:NewToggle("Toggle ESP", "Bật hoặc tắt ESP", function(state)
    toggleESP(state)
end)

