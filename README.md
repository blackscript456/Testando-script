-- Coloque esse script dentro de StarterPlayerScripts como LocalScript

local Players = game:GetService("Players")
local player = Players.LocalPlayer
local mouse = player:GetMouse()

-- Criar GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PlayerTagGui"
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Função para criar o botão círculo preto e a interface interna
local function createToggleButton()
    -- Botão círculo preto
    local circleButton = Instance.new("Frame")
    circleButton.Name = "CircleButton"
    circleButton.Size = UDim2.new(0, 50, 0, 50)
    circleButton.Position = UDim2.new(0, 20, 0, 20)
    circleButton.BackgroundColor3 = Color3.new(0, 0, 0)
    circleButton.BorderSizePixel = 0
    circleButton.AnchorPoint = Vector2.new(0, 0)
    circleButton.ClipsDescendants = true
    circleButton.Parent = screenGui
    circleButton.ZIndex = 5

    -- Deixar o Frame circular com um UIStroke e Corner
    local uICorner = Instance.new("UICorner")
    uICorner.CornerRadius = UDim.new(1,0)
    uICorner.Parent = circleButton

    -- Interface que será aberta e fechada
    local interfaceFrame = Instance.new("Frame")
    interfaceFrame.Name = "InterfaceFrame"
    interfaceFrame.Size = UDim2.new(0, 200, 0, 150)
    interfaceFrame.Position = UDim2.new(0, 20, 0, 75)
    interfaceFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    interfaceFrame.BorderSizePixel = 0
    interfaceFrame.Visible = false
    interfaceFrame.Parent = screenGui
    interfaceFrame.ZIndex = 4

    -- Exemplo de conteúdo da interface
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -20, 0, 50)
    label.Position = UDim2.new(0, 10, 0, 10)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.new(1, 1, 1)
    label.Font = Enum.Font.SourceSansBold
    label.TextSize = 22
    label.Text = "Interface de jogador"
    label.Parent = interfaceFrame

    -- Variável para estado aberto/fechado
    local open = false

    -- Função para alternar interface
    local function toggleInterface()
        open = not open
        interfaceFrame.Visible = open
    end

    -- Evento de clique para alternar interface
    circleButton.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            toggleInterface()
        end
    end)

    -- Função para permitir arrastar o botão
    local dragging
    local dragInput
    local dragStart
    local startPos

    local function update(input)
        local delta = input.Position - dragStart
        circleButton.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        interfaceFrame.Position = UDim2.new(0, circleButton.Position.X.Offset, 0, circleButton.Position.Y.Offset + 55)
    end

    circleButton.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton2 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = circleButton.Position

            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    circleButton.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)

    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            update(input)
        end
    end)
end

-- Função para criar BillboardGui para o personagem do jogador com o nome em vermelho
local function createNameTag(player)
    local character = player.Character or player.CharacterAdded:Wait()
    local head = character:WaitForChild("Head")

    -- Criar BillboardGui
    local billboard = Instance.new("BillboardGui")
    billboard.Name = "NameTag"
    billboard.Adornee = head
    billboard.Size = UDim2.new(0, 100, 0, 40)
    billboard.AlwaysOnTop = true
    billboard.MaxDistance = 100
    billboard.Parent = player:WaitForChild("PlayerGui") -- só visível do próprio jogador

    -- Fundo transparente
    local frame = Instance.new("Frame")
    frame.BackgroundTransparency = 1
    frame.Size = UDim2.new(1, 0, 1, 0)
    frame.Parent = billboard

    -- Texto do nome em vermelho
    local textLabel = Instance.new("TextLabel")
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.TextColor3 = Color3.new(1, 0, 0)
    textLabel.TextStrokeColor3 = Color3.new(0, 0, 0)
    textLabel.TextStrokeTransparency = 0.5
    textLabel.Font = Enum.Font.SourceSansBold
    textLabel.TextSize = 18
    textLabel.Text = player.Name
    textLabel.Parent = frame
end

-- Criar tag para o jogador local
createNameTag(player)
-- Criar a interface com o botão
createToggleButton()
