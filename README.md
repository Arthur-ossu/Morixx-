-- Morixx Hub v2 – Estilo Redz Hub
-- Compatível com Delta, Hydrogen, Fluxus, KRNL

-- Configurações
_G.AutoFarm = false
_G.AutoHaki = false
_G.AutoSkills = false
_G.FarmRadius = 10 -- distância para teleporte antes de atacar

-- Função para teleporte suave ao mob
local TweenService = game:GetService("TweenService")
local function TeleportTo(cframe)
    local hrp = game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if hrp then
        TweenService:Create(hrp, TweenInfo.new(0.3), {CFrame = cframe * CFrame.new(0, 3, -3)}):Play()
    end
end

-- Função de combate aprimorada
local function FarmRoutine()
    while _G.AutoFarm do
        wait(0.3)
        local player = game.Players.LocalPlayer
        local char = player.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then break end

        for _, mob in pairs(workspace.Enemies:GetChildren()) do
            local humanoid = mob:FindFirstChild("Humanoid")
            local mhrp = mob:FindFirstChild("HumanoidRootPart")
            if humanoid and mhrp and humanoid.Health > 0 then
                local dist = (hrp.Position - mhrp.Position).Magnitude
                if dist > _G.FarmRadius then
                    TeleportTo(mhrp.CFrame)
                    wait(0.4)
                end

                if _G.AutoHaki then
                    if not char:FindFirstChild("HasBuso") then
                        game:GetService("VirtualInputManager"):SendKeyEvent(true, "J", false, game)
                        wait(0.8)
                        game:GetService("VirtualInputManager"):SendKeyEvent(false, "J", false, game)
                    end
                end

                -- Loop de ataque
                repeat
                    if _G.AutoSkills then
                        for _, key in ipairs({"Z","X","C","V"}) do
                            game:GetService("VirtualInputManager"):SendKeyEvent(true, key, false, game)
                            wait(0.15)
                            game:GetService("VirtualInputManager"):SendKeyEvent(false, key, false, game)
                        end
                    end
                    if char:FindFirstChildOfClass("Tool") then
                        char:FindFirstChildOfClass("Tool"):Activate()
                    end
                    wait(0.2)
                until humanoid.Health <= 0 or not _G.AutoFarm

                wait(0.5)
            end
        end
    end
end

-- GUI Leve
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 260, 0, 310)
Frame.Position = UDim2.new(0.5, -130, 0.5, -155)
Frame.BackgroundColor3 = Color3.fromRGB(18,18,18)
Frame.BorderSizePixel = 0
Frame.Active = true
Frame.Draggable = true

local title = Instance.new("TextLabel", Frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "Morixx Hub v2"
title.TextColor3 = Color3.new(1,1,1)
title.BackgroundColor3 = Color3.fromRGB(28,28,28)
title.Font = Enum.Font.GothamBold
title.TextSize = 22

local function CreateButton(text, pos, callback)
    local btn = Instance.new("TextButton", Frame)
    btn.Size = UDim2.new(0.9, 0, 0, 40)
    btn.Position = UDim2.new(0.05, 0, 0, pos)
    btn.Text = text
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 18
    btn.TextColor3 = Color3.new(1,1,1)
    btn.BackgroundColor3 = Color3.fromRGB(45,45,45)
    btn.MouseButton1Click:Connect(callback)
end

CreateButton("➤ AutoFarm: OFF", 60, function(self)
    _G.AutoFarm = not _G.AutoFarm
    self.Text = "➤ AutoFarm: " .. ( _G.AutoFarm and "ON" or "OFF")
    if _G.AutoFarm then spawn(FarmRoutine) end
end)
CreateButton("➤ AutoHaki: OFF", 115, function(self)
    _G.AutoHaki = not _G.AutoHaki
    self.Text = "➤ AutoHaki: " .. ( _G.AutoHaki and "ON" or "OFF")
end)
CreateButton("➤ AutoSkills: OFF", 170, function(self)
    _G.AutoSkills = not _G.AutoSkills
    self.Text = "➤ AutoSkills: " .. ( _G.AutoSkills and "ON" or "OFF")
end)
CreateButton("Fechar Morixx Hub", 245, function()
    ScreenGui:Destroy()
end)
