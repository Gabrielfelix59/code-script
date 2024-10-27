local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local playerBackpack = player:WaitForChild("Backpack")
local user = game.Players.LocalPlayer
local Character = user.Character or user.CharacterAdded:Wait()

-- Criar a interface
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local TitleLabel = Instance.new("TextLabel")
local AutoFarmButton = Instance.new("TextButton")
local StatusLabel = Instance.new("TextLabel")
local MessageLabel = Instance.new("TextLabel")
local LevelInput = Instance.new("TextBox")
local UICorner = Instance.new("UICorner")
local FastAttackButton = Instance.new("TextButton")
local AutoClickButton = Instance.new("TextButton")
local ProgressBar = Instance.new("Frame")

local farming = false
local fastAttack = false
local autoClick = false
local currentLevel = 0

-- Configurações da interface
ScreenGui.Parent = playerGui
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- Fundo preto
MainFrame.Position = UDim2.new(0.1, 0, 0.1, 0)
MainFrame.Size = UDim2.new(0, 400, 0, 400)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true -- Permite arrastar a interface

-- Adicionando contorno RGB
local OutlineFrame = Instance.new("Frame")
OutlineFrame.Parent = MainFrame
OutlineFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- Fundo preto
OutlineFrame.BorderSizePixel = 0
OutlineFrame.Size = UDim2.new(1, 6, 1, 6) -- Contorno
OutlineFrame.Position = UDim2.new(-0.03, 0, -0.03, 0)

UICorner.Parent = OutlineFrame

-- Efeito de contorno RGB
local function rainbowOutline()
    while farming do
        for _, color in pairs({Color3.fromRGB(255, 0, 0), Color3.fromRGB(0, 255, 0), Color3.fromRGB(0, 0, 255), Color3.fromRGB(255, 255, 0), Color3.fromRGB(255, 0, 255), Color3.fromRGB(0, 255, 255)}) do
            OutlineFrame.BackgroundColor3 = color
            wait(0.5) -- Tempo entre as trocas de cor
        end
    end
end

TitleLabel.Parent = MainFrame
TitleLabel.BackgroundTransparency = 1
TitleLabel.Size = UDim2.new(0, 400, 0, 30)
TitleLabel.Position = UDim2.new(0, 0, 0, 10)
TitleLabel.Text = "Auto Farm Level - Blox Fruits"
TitleLabel.TextColor3 = Color3.fromRGB(255, 215, 0)
TitleLabel.TextSize = 22
TitleLabel.Font = Enum.Font.SourceSansBold

AutoFarmButton.Parent = MainFrame
AutoFarmButton.BackgroundColor3 = Color3.fromRGB(70, 130, 180)
AutoFarmButton.Size = UDim2.new(0, 380, 0, 40)
AutoFarmButton.Position = UDim2.new(0, 10, 0, 50)
AutoFarmButton.Text = "Iniciar Auto Farm"
AutoFarmButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoFarmButton.TextSize = 16
AutoFarmButton.Font = Enum.Font.SourceSansBold

StatusLabel.Parent = MainFrame
StatusLabel.BackgroundTransparency = 1
StatusLabel.Size = UDim2.new(0, 380, 0, 20)
StatusLabel.Position = UDim2.new(0, 10, 0, 100)
StatusLabel.Text = "Status: Inativo"
StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
StatusLabel.TextSize = 14

MessageLabel.Parent = MainFrame
MessageLabel.BackgroundTransparency = 1
MessageLabel.Size = UDim2.new(0, 380, 0, 20)
MessageLabel.Position = UDim2.new(0, 10, 0, 130)
MessageLabel.TextColor3 = Color3.fromRGB(255, 215, 0)
MessageLabel.TextSize = 14

LevelInput.Parent = MainFrame
LevelInput.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
LevelInput.Size = UDim2.new(0, 380, 0, 30)
LevelInput.Position = UDim2.new(0, 10, 0, 170)
LevelInput.Text = "Nível Desejado"
LevelInput.TextColor3 = Color3.fromRGB(255, 255, 255)
LevelInput.TextSize = 14
LevelInput.Font = Enum.Font.SourceSans

FastAttackButton.Parent = MainFrame
FastAttackButton.BackgroundColor3 = Color3.fromRGB(255, 69, 0) -- Vermelho
FastAttackButton.Size = UDim2.new(0, 380, 0, 40)
FastAttackButton.Position = UDim2.new(0, 10, 0, 210)
FastAttackButton.Text = "Ativar Fast Attack"
FastAttackButton.TextColor3 = Color3.fromRGB(255, 255, 255)
FastAttackButton.TextSize = 16
FastAttackButton.Font = Enum.Font.SourceSansBold

AutoClickButton.Parent = MainFrame
AutoClickButton.BackgroundColor3 = Color3.fromRGB(0, 191, 255) -- Azul
AutoClickButton.Size = UDim2.new(0, 380, 0, 40)
AutoClickButton.Position = UDim2.new(0, 10, 0, 260)
AutoClickButton.Text = "Ativar Auto Click"
AutoClickButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoClickButton.TextSize = 16
AutoClickButton.Font = Enum.Font.SourceSansBold

ProgressBar.Parent = MainFrame
ProgressBar.BackgroundColor3 = Color3.fromRGB(0, 255, 0) -- Verde
ProgressBar.Size = UDim2.new(0, 0, 0, 20)
ProgressBar.Position = UDim2.new(0, 10, 0, 310)

local function moveTo(position)
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        player.Character.HumanoidRootPart.CFrame = CFrame.new(position)
    end
end

local function attackEnemy(enemy)
    if enemy and enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
        local tool = playerBackpack:FindFirstChildOfClass("Tool")
        if tool then
            player.Character.HumanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5)
            wait(fastAttack and 0.1 or 0.5) -- Aguarda menos tempo se Fast Attack estiver ativado
            tool:Activate() -- Ataca o inimigo
            MessageLabel.Text = "Atacando: " .. enemy.Name
        end
    end
end

local function acceptMission(missionNPC)
    if missionNPC and missionNPC:FindFirstChild("ProximityPrompt") then
        missionNPC.ProximityPrompt:Invoke()
        MessageLabel.Text = "Missão aceita: " .. missionNPC.Name
    end
end

local function getTargetLevel()
    local level = tonumber(LevelInput.Text)
    if level then
        return level
    else
        return 100 -- Valor padrão caso a entrada não seja válida
    end
end

local function autoFarm()
    while farming do
        local missionNPC = game.Workspace:FindFirstChild("NomeDoNPC") -- Substitua "NomeDoNPC" pelo nome do NPC
        if missionNPC then
            moveTo(missionNPC.Position)
            acceptMission(missionNPC)

            local targetLevel = getTargetLevel()

            while farming and player:WaitForChild("Data").Level.Value < targetLevel do
                currentLevel = player:WaitForChild("Data").Level.Value
                ProgressBar.Size = UDim2.new(currentLevel / targetLevel, 0, 0, 20) -- Atualiza a barra de progresso

                for _, enemy in pairs(workspace:GetChildren()) do
                    if enemy:IsA("Model") and enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                        if enemy.Name:lower() == "nome_do_inimigo" then -- Substitua pelo nome do inimigo
                            attackEnemy(enemy)
                            wait(autoClick and 0.1 or 1) -- Espera menos tempo se Auto Click estiver ativado
                        end
                    end
                end
                wait(2) -- Espera 2 segundos antes de verificar novamente
            end
            
            MessageLabel.Text = "Nível atingido! Pare o farm."
            farming = false
            StatusLabel.Text = "Status: Inativo"
            ProgressBar.Size = UDim2.new(0, 0, 0, 20) -- Reseta a barra de progresso
        else
            MessageLabel.Text = "NPC não encontrado!"
            wait(2) -- Espera 2 segundos antes de verificar novamente
        end
        wait(1)
    end
end

AutoFarmButton.MouseButton1Click:Connect(function()
    farming = not farming
    AutoFarmButton.Text = farming and "Parar Auto Farm" or "Iniciar Auto Farm"
    StatusLabel.Text = "Status: " .. (farming and "Ativo" or "Inativo")

    if farming then
        rainbowOutline() -- Inicia o efeito de contorno RGB
        autoFarm()
    end
end)

FastAttackButton.MouseButton1Click:Connect(function()
    fastAttack = not fastAttack
    FastAttackButton.Text = fastAttack and "Desativar Fast Attack" or "Ativar Fast Attack"
end)

AutoClickButton.MouseButton1Click:Connect(function()
    autoClick = not autoClick
    AutoClickButton.Text = autoClick and "Desativar Auto Click" or "Ativar Auto Click"
end)

-- Inicialização
StatusLabel.Text = "Status: Inativo"
