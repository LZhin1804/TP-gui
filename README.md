local Player = game:GetService("Players").LocalPlayer
local Mouse = Player:GetMouse()
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

-- // COMPATIBILIDADE DE GUI (Universal)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "XenoMouse_TP"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Tenta CoreGui, se não der (comum em Mobile/Exploits básicos), vai para PlayerGui
local success, err = pcall(function()
    ScreenGui.Parent = game:GetService("CoreGui")
end)
if not success then
    ScreenGui.Parent = Player:WaitForChild("PlayerGui")
end

-- // INTERFACE
local Frame = Instance.new("Frame", ScreenGui)
local Title = Instance.new("TextLabel", Frame)
local BindBtn = Instance.new("TextButton", Frame)
local Info = Instance.new("TextLabel", Frame)

Frame.Size = UDim2.new(0, 180, 0, 100)
Frame.Position = UDim2.new(0.1, 0, 0.4, 0)
Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Frame.BorderSizePixel = 2
Frame.BorderColor3 = Color3.fromRGB(60, 0, 180)
Frame.Active = true
Frame.Draggable = true -- Ativa o arrastar nativo (funciona em quase todos os executores)

Title.Size = UDim2.new(1, 0, 0, 25)
Title.Text = "INSTANTE TP"
Title.BackgroundColor3 = Color3.fromRGB(60, 0, 180)
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 13

BindBtn.Size = UDim2.new(0.9, 0, 0, 30)
BindBtn.Position = UDim2.new(0.05, 0, 0.35, 0)
BindBtn.Text = "Tecla: [T]"
BindBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
BindBtn.TextColor3 = Color3.new(1, 1, 1)
BindBtn.Font = Enum.Font.SourceSans

Info.Size = UDim2.new(1, 0, 0, 20)
Info.Position = UDim2.new(0, 0, 0.75, 0)
Info.Text = "Aponte e use a tecla"
Info.TextColor3 = Color3.new(0.7, 0.7, 0.7)
Info.BackgroundTransparency = 1
Info.TextSize = 11

-- // LÓGICA DE TELEPORTE ROBUSTA
local teleportKey = Enum.KeyCode.T
local isSettingKey = false

local function teletransportar()
    local char = Player.Character
    if char then
        -- Tenta RootPart, se não tiver (em alguns jogos), tenta o Head
        local root = char:FindFirstChild("HumanoidRootPart") or char:FindFirstChild("Head")
        if root and Mouse.Hit then
            -- Usamos PivotTo para evitar que o personagem quebre ou morra em certos jogos
            char:PivotTo(CFrame.new(Mouse.Hit.Position + Vector3.new(0, 3, 0)))
        end
    end
end

-- Detectar Tecla
UserInputService.InputBegan:Connect(function(input, processed)
    if processed then return end 

    if isSettingKey then
        if input.UserInputType == Enum.UserInputType.Keyboard then
            teleportKey = input.KeyCode
            BindBtn.Text = "Tecla: [" .. input.KeyCode.Name .. "]"
            isSettingKey = false
            BindBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
        end
        return
    end

    if input.KeyCode == teleportKey then
        teletransportar()
    end
end)

-- Suporte para Mobile (Clique no botão para TP se estiver no Celular)
BindBtn.MouseButton1Click:Connect(function()
    if UserInputService.TouchEnabled and not UserInputService.KeyboardEnabled then
        teletransportar()
    else
        isSettingKey = true
        BindBtn.Text = "Pressione uma Tecla..."
        BindBtn.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
    end
end)

-- Notificação de carregamento
print("INSTANTE TP carregado com sucesso!")
