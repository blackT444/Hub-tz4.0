-- Criar tela principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PlayerESP"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Frame para botão ON/OFF
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0.15, 0, 0.07, 0) -- Largura de 15% e altura de 7%
Frame.Position = UDim2.new(0.4, 0, 0.85, 0)
Frame.BackgroundTransparency = 0.5
Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Frame.Parent = ScreenGui

local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(0.45, 0, 1, 0) -- Largura do botão ON/OFF
ToggleButton.BackgroundTransparency = 1
ToggleButton.TextScaled = true
ToggleButton.Text = "ON"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.Parent = Frame

-- Botão para ligar/desligar Speed
local SpeedButton = Instance.new("TextButton")
SpeedButton.Size = UDim2.new(0.45, 0, 1, 0) -- Largura do botão Speed
SpeedButton.Position = UDim2.new(0.55, 0, 0, 0) -- Posição ao lado do botão ON/OFF
SpeedButton.BackgroundTransparency = 1
SpeedButton.TextScaled = true
SpeedButton.Text = "F" -- Inicialmente desativado
SpeedButton.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedButton.Parent = Frame

-- Variável para controlar a visibilidade do ESP
local showESP = true

-- Função para criar ESP no jogador
local function createESP(player)
    if player.Character and player.Character:FindFirstChild("Head") then
        -- Criar Highlight para linha em volta do jogador
        local highlight = Instance.new("Highlight")
        highlight.Name = "ESP_Highlight"
        highlight.Parent = player.Character
        highlight.FillTransparency = 1
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255) -- Cor branca para melhor visibilidade
        highlight.OutlineTransparency = 0
        highlight.Enabled = showESP -- Habilita ou desabilita com base no estado
    end
end

-- Adicionar ESP a todos os jogadores
local function addESPToPlayers()
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer then
            createESP(player)
        end
    end
end

-- Conectar eventos para novos jogadores
game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        wait(1)
        createESP(player)
    end)
end)

-- Atualizar ESP de jogadores já presentes
addESPToPlayers()

-- Função para atualizar o ESP e Highlight
local function updateESP()
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer and player.Character then
            local highlight = player.Character:FindFirstChild("ESP_Highlight")
            if highlight then
                highlight.Enabled = showESP -- Atualiza a visibilidade do highlight
            end
        end
    end
end

-- Alternar visibilidade do ESP
local function toggleESP()
    showESP = not showESP
    updateESP() -- Atualiza a visibilidade do ESP
    ToggleButton.Text = showESP and "ON" or "OFF"
end

ToggleButton.MouseButton1Click:Connect(toggleESP)

-- Ajuste de iluminação sem exagero
local function adjustLighting()
    local lighting = game.Lighting

    lighting.Brightness = 1.5
    lighting.Ambient = Color3.fromRGB(100, 100, 100)
    lighting.OutdoorAmbient = Color3.fromRGB(180, 180, 180)

    -- Criar luz no jogador para iluminar locais escuros
    local player = game.Players.LocalPlayer
    if player.Character then
        local head = player.Character:FindFirstChild("Head")
        if head then
            local light = head:FindFirstChild("ESP_Light")
            if not light then
                light = Instance.new("PointLight")
                light.Name = "ESP_Light"
                light.Parent = head
                light.Brightness = 2
                light.Range = 200 -- Aumentado alcance da luz para 200 unidades
                light.Color = Color3.fromRGB(255, 255, 255)
            end
        end
    end
end

-- Atualizar iluminação a cada 0.5s
game:GetService("RunService").RenderStepped:Connect(adjustLighting)

-- Ajustar velocidade do jogador
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

-- Variável para controlar a velocidade
local walkSpeed = 16 -- Velocidade padrão

-- Função para ativar/desativar Speed
local speedEnabled = false
SpeedButton.MouseButton1Click:Connect(function()
    speedEnabled = not speedEnabled
    if speedEnabled then
        walkSpeed = 24 -- Configura a velocidade para 24
        SpeedButton.Text = "S" -- Ativado
    else
        walkSpeed = 16 -- Velocidade padrão
        SpeedButton.Text = "F" -- Desativado
    end

    -- Aplicar a velocidade ao Humanoid
    character:WaitForChild("Humanoid").WalkSpeed = walkSpeed
end)

-- Atualizar a velocidade enquanto o jogador pula
character:WaitForChild("Humanoid").Jumping:Connect(function()
    character:WaitForChild("Humanoid").WalkSpeed = walkSpeed
end)
