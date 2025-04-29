# Cartola-hub-fake
Buy hub script
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = game.Players.LocalPlayer.PlayerGui

-- Fundo transparente com roxo bonito (gradiente)
local imageLabel = Instance.new("ImageLabel")
imageLabel.Size = UDim2.new(1, 0, 1, 0)  -- Cobre toda a tela
imageLabel.Position = UDim2.new(0, 0, 0, 0)
imageLabel.BackgroundTransparency = 1  -- Deixa o fundo transparente
imageLabel.Image = ""  -- Nenhuma imagem, apenas cor
imageLabel.Parent = screenGui

-- Gradiente roxo
local gradient = Instance.new("UIGradient")
gradient.Parent = imageLabel
gradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(128, 0, 255)),  -- Cor roxa
    ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 0, 255))  -- Roxo mais claro no final
})
gradient.Rotation = 45  -- Define a direção do gradiente (inclinação)

-- Botão de Voo
local flyButton = Instance.new("TextButton")
flyButton.Size = UDim2.new(0, 250, 0, 50)
flyButton.Position = UDim2.new(0.5, -125, 0.6, 0)
flyButton.BackgroundTransparency = 0.5
flyButton.BackgroundColor3 = Color3.fromRGB(128, 0, 255)
flyButton.BorderSizePixel = 4
flyButton.BorderColor3 = Color3.fromRGB(255, 0, 255)
flyButton.Text = "Ativar Voo"
flyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
flyButton.TextSize = 24
flyButton.Parent = screenGui

-- Botão de Fechar a GUI
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 150, 0, 50)
closeButton.Position = UDim2.new(0.5, -75, 0.7, 0)
closeButton.BackgroundTransparency = 0.5
closeButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)  -- Cor vermelha para fechar
closeButton.BorderSizePixel = 4
closeButton.BorderColor3 = Color3.fromRGB(255, 0, 0)
closeButton.Text = "Fechar GUI"
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.TextSize = 24
closeButton.Parent = screenGui

-- Criando a ferramenta TPTool (Teletransporte)
local tool = Instance.new("Tool")
tool.Name = "TPTool"
tool.RequiresHandle = true  -- Necessário ter uma "handle" para ser usado como ferramenta
tool.Parent = game.Players.LocalPlayer.Backpack  -- Ferramenta adicionada ao inventário do jogador

-- Criando a handle da ferramenta (parte visível da ferramenta)
local handle = Instance.new("Part")
handle.Size = Vector3.new(1, 5, 1)  -- Tamanho da ferramenta (como um bastão)
handle.Position = Vector3.new(0, 0, 0)  -- Posição da handle
handle.Anchored = false  -- A handle não pode ser ancorada
handle.CanCollide = false  -- A handle não pode colidir
handle.BrickColor = BrickColor.new("Bright violet")  -- Cor roxa
handle.Parent = tool  -- A handle pertence à ferramenta

-- Função para teletransporte
local function teleportPlayer()
    local player = game.Players.LocalPlayer
    local character = player.Character
    if character then
        -- Pontos de teletransporte (você deve adicionar essas partes no mapa)
        local teleportLocation1 = workspace:WaitForChild("TeleportLocation1")
        local teleportLocation2 = workspace:WaitForChild("TeleportLocation2")

        if character.HumanoidRootPart then
            if teleportLocation1 and teleportLocation2 then
                -- Alterna entre os dois pontos de teletransporte
                if (character.HumanoidRootPart.Position - teleportLocation1.Position).Magnitude < 10 then
                    character:SetPrimaryPartCFrame(teleportLocation2.CFrame)
                else
                    character:SetPrimaryPartCFrame(teleportLocation1.CFrame)
                end
            end
        end
    end
end

tool.Activated:Connect(function()
    teleportPlayer()  -- Chama a função de teletransporte
end)

-- Função de voo
local flying = false
local speed = 50
local character = game.Players.LocalPlayer.Character or game.Players.LocalPlayer.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local bodyVelocity = nil
local bodyGyro = nil

-- Função para ativar o voo
local function activateFlight()
    if not flying then
        flying = true
        humanoid.PlatformStand = true

        bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = Vector3.new(5000, 5000, 5000)
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.Parent = character:WaitForChild("HumanoidRootPart")

        bodyGyro = Instance.new("BodyGyro")
        bodyGyro.MaxTorque = Vector3.new(5000, 5000, 5000)
        bodyGyro.CFrame = character.HumanoidRootPart.CFrame
        bodyGyro.Parent = character:WaitForChild("HumanoidRootPart")

        -- Atualiza a velocidade do voo
        game:GetService("RunService").Heartbeat:Connect(function()
            if flying then
                bodyVelocity.Velocity = Vector3.new(0, speed, 0)
            end
        end)
    end
end

-- Função para desativar o voo
local function deactivateFlight()
    if flying then
        flying = false
        humanoid.PlatformStand = false

        if bodyVelocity then
            bodyVelocity:Destroy()
        end
        if bodyGyro then
            bodyGyro:Destroy()
        end
    end
end

-- Quando o botão de voo for clicado
flyButton.MouseButton1Click:Connect(function()
    if flying then
        deactivateFlight()
        flyButton.Text = "Ativar Voo"
    else
        activateFlight()
        flyButton.Text = "Desativar Voo"
        
        -- Mensagem de ativação do script
        local message = Instance.new("TextLabel")
        message.Size = UDim2.new(0, 300, 0, 50)
        message.Position = UDim2.new(0.5, -150, 0.4, 0)
        message.BackgroundTransparency = 0.5
        message.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        message.Text = "Voo Ativado! Divirta-se!"
        message.TextColor3 = Color3.fromRGB(255, 255, 255)
        message.TextSize = 24
        message.Parent = screenGui

        -- Destroi a mensagem após 3 segundos
        game:GetService("Debris"):AddItem(message, 3)
    end
end)

-- Quando o botão de fechar for clicado
closeButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()  -- Fecha e destrói a GUI
end)
