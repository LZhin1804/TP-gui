local Player = game:GetService("Players").LocalPlayer
local Mouse = Player:GetMouse()
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

-- // PROTEÇÃO CONTRA DETECÇÃO BÁSICA
local ScreenGui = Instance.new("ScreenGui")
-- Tenta colocar na CoreGui (esconde de capturas de tela), se falhar vai para PlayerGui
local success, err = pcall(function()
    ScreenGui.Parent = game:GetService("CoreGui")
end)
if not success then
    ScreenGui.Parent = Player:WaitForChild("PlayerGui")
end
ScreenGui.Name = "XenoMouse_TP"

-- // INTERFACE
local Frame = Instance.new("Frame", ScreenGui)
local Title = Instance.new("TextLabel", Frame)
local BindBtn = Instance.new("TextButton", Frame)
local Info = Instance.new("TextLabel", Frame)

-- Estilo do Menu
Frame.Size = UDim2.new(0, 200, 0, 120)
Frame.Position = UDim2.new(0.05, 0, 0.4, 0)
Frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Frame.BorderSizePixel = 0
Frame.Active = true

Title.Size = UDim2.new(1, 0, 0, 30)
Title.Text = "XENO MOUSE TP"
Title.BackgroundColor3 = Color3.fromRGB(60, 0, 180)
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 14

BindBtn.Size = UDim2.new(0.8, 0, 0, 35)
BindBtn.Position = UDim2.new(0.1, 0, 0.35, 0)
BindBtn.Text = "Tecla: [T]"
BindBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
BindBtn.TextColor3 = Color3.new(1, 1, 1)
BindBtn.BorderSizePixel = 0

Info.Size = UDim2.new(1, 0, 0, 25)
Info.Position = UDim2.new(0, 0, 0.75, 0)
Info.Text = "Aponte e use a tecla"
Info.TextColor3 = Color3.new(0.8, 0.8, 0.8)
Info.BackgroundTransparency = 1
Info.TextSize = 12

-- // FUNÇÃO DE ARRASTAR (UNIVERSAL)
local dragging, dragInput, dragStart, startPos
Frame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = Frame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then dragging = false end
        end)
    end
end)
Frame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)
RunService.RenderStepped:Connect(function()
    if dragging and dragInput then
        local delta = dragInput.Position - dragStart
        Frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- // LÓGICA DO TELEPORTE
local teleportKey = Enum.KeyCode.T
local isSettingKey = false

local function teletransportar()
    local char = Player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if root then
        -- O CFrame.new(pos) move o personagem mantendo a orientação
        root.CFrame = CFrame.new(Mouse.Hit.Position + Vector3.new(0, 3, 0))
    end
end

-- Detectar Entrada
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

-- Botão Config
BindBtn.MouseButton1Click:Connect(function()
    isSettingKey = true
    BindBtn.Text = "Pressione uma Tecla..."
    BindBtn.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
end)
