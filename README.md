local Player = game.Players.LocalPlayer
local Mouse = Player:GetMouse()
local UserInputService = game:GetService("UserInputService")

-- // INTERFACE (GUI)
local ScreenGui = Instance.new("ScreenGui", game:GetService("CoreGui"))
local Frame = Instance.new("Frame", ScreenGui)
local Title = Instance.new("TextLabel", Frame)
local BindBtn = Instance.new("TextButton", Frame)
local Info = Instance.new("TextLabel", Frame)

-- Estilo do Menu
Frame.Size = UDim2.new(0, 200, 0, 120)
Frame.Position = UDim2.new(0.05, 0, 0.4, 0)
Frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Frame.Active = true
Frame.Draggable = true -- Você pode arrastar o menu

Title.Size = UDim2.new(1, 0, 0, 30)
Title.Text = "XENO MOUSE TP"
Title.BackgroundColor3 = Color3.fromRGB(60, 0, 180)
Title.TextColor3 = Color3.new(1, 1, 1)

BindBtn.Size = UDim2.new(0.8, 0, 0, 35)
BindBtn.Position = UDim2.new(0.1, 0, 0.35, 0)
BindBtn.Text = "Tecla: [T]"
BindBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
BindBtn.TextColor3 = Color3.new(1, 1, 1)

Info.Size = UDim2.new(1, 0, 0, 25)
Info.Position = UDim2.new(0, 0, 0.75, 0)
Info.Text = "Aponte o mouse e use a tecla"
Info.TextColor3 = Color3.new(0.8, 0.8, 0.8)
Info.BackgroundTransparency = 1

-- // LÓGICA DO SCRIPT
local teleportKey = Enum.KeyCode.T
local isSettingKey = false

-- Função que faz o Teleporte para a posição do Mouse
local function teletransportar()
    local character = Player.Character
    if character and character:FindFirstChild("HumanoidRootPart") then
        -- Mouse.Hit.Position pega o local exato onde o cursor toca o chão/objeto
        -- Adicionamos +3 no eixo Y para você não ficar preso dentro do chão
        character.HumanoidRootPart.CFrame = CFrame.new(Mouse.Hit.Position + Vector3.new(0, 3, 0))
    end
end

-- Detectar Tecla
UserInputService.InputBegan:Connect(function(input, processed)
    if processed then return end -- Não ativa se você estiver digitando no chat

    -- Se estivermos configurando uma nova tecla
    if isSettingKey then
        if input.UserInputType == Enum.UserInputType.Keyboard then
            teleportKey = input.KeyCode
            BindBtn.Text = "Tecla: [" .. input.KeyCode.Name .. "]"
        elseif input.UserInputType == Enum.UserInputType.MouseButton1 then
            teleportKey = Enum.UserInputType.MouseButton1 -- Aceita click também
            BindBtn.Text = "Tecla: [CLICK ESQUERDO]"
        end
        isSettingKey = false
        BindBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
        return
    end

    -- Executar TP se a tecla/click for igual ao configurado
    if input.KeyCode == teleportKey or input.UserInputType == teleportKey then
        teletransportar()
    end
end)

-- Botão para mudar a tecla
BindBtn.MouseButton1Click:Connect(function()
    isSettingKey = true
    BindBtn.Text = "Pressione algo..."
    BindBtn.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
end)
