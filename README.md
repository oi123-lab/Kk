local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/jensonhirst/Orion/main/source')))()
local Window = OrionLib:MakeWindow({Name = "MATRIX HUB GERAR ILHAS TEST", HidePremium = false, SaveConfig = true, ConfigFolder = "OrionTest"})

--[[
Name = <string> - The name of the UI.
HidePremium = <bool> - Whether or not the user details shows Premium status or not.
SaveConfig = <bool> - Toggles the config saving in the UI.
ConfigFolder = <string> - The name of the folder where the configs are saved.
IntroEnabled = <bool> - Whether or not to show the intro animation.
IntroText = <string> - Text to show in the intro animation.
IntroIcon = <string> - URL to the image you want to use in the intro animation.
Icon = <string> - URL to the image you want displayed on the window.
CloseCallback = <function> - Function to execute when the window is closed.
]]


local Tab = Window:MakeTab({
	Name = "Ilhas Adm",
	Icon = "rbxassetid://18319058691",
	PremiumOnly = false
})


-- VariÃƒÂ¡veis
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer

-- PosiÃƒÂ§ÃƒÂ£o do mapa secreto
local mapPosition = Vector3.new(10000, 500, 10000)

-- FunÃƒÂ§ÃƒÂ£o para gerar a ilha
local function generateIsland()
    -- Criar o chÃƒÂ£o de grama
    local grass = Instance.new("Part")
    grass.Size = Vector3.new(200, 2, 200)
    grass.Position = mapPosition
    grass.Anchored = true
    grass.Material = Enum.Material.Grass
    grass.Color = Color3.fromRGB(34, 139, 34)
    grass.Parent = game.Workspace

    -- Criar ÃƒÂ¡rvores arredondadas
    local function createTree(position)
        local trunk = Instance.new("Part")
        trunk.Size = Vector3.new(2, 8, 2)
        trunk.Position = position
        trunk.Anchored = true
        trunk.Material = Enum.Material.Wood
        trunk.Color = Color3.fromRGB(139, 69, 19)
        trunk.Parent = game.Workspace

        local leaves = Instance.new("Part")
        leaves.Size = Vector3.new(8, 8, 8)
        leaves.Position = position + Vector3.new(0, 6, 0)
        leaves.Anchored = true
        leaves.Shape = Enum.PartType.Ball
        leaves.Material = Enum.Material.Grass
        leaves.Color = Color3.fromRGB(34, 139, 34)
        leaves.Parent = game.Workspace
    end

    -- Posicionamento das ÃƒÂ¡rvores
    local treePositions = {
        mapPosition + Vector3.new(60, 1, 60),
        mapPosition + Vector3.new(-60, 1, 60),
        mapPosition + Vector3.new(60, 1, -60),
        mapPosition + Vector3.new(-60, 1, -60),
        mapPosition + Vector3.new(30, 1, 70),
        mapPosition + Vector3.new(-30, 1, 70),
        mapPosition + Vector3.new(50, 1, 30),
        mapPosition + Vector3.new(-50, 1, -30),
        mapPosition + Vector3.new(20, 1, -60),
        mapPosition + Vector3.new(-20, 1, 50),
        mapPosition + Vector3.new(70, 1, -20),
        mapPosition + Vector3.new(-70, 1, 20),
    }

    for _, pos in pairs(treePositions) do
        createTree(pos)
    end

    -- Criar muros ao redor da ilha
    local function createWall(position, size)
        local wall = Instance.new("Part")
        wall.Size = size
        wall.Position = position
        wall.Anchored = true
        wall.Material = Enum.Material.Brick
        wall.Color = Color3.fromRGB(150, 75, 0)
        wall.Parent = game.Workspace
    end

    -- Criando os muros
    createWall(mapPosition + Vector3.new(0, 6, 101), Vector3.new(200, 12, 2)) -- Frente
    createWall(mapPosition + Vector3.new(0, 6, -101), Vector3.new(200, 12, 2)) -- TrÃƒÂ¡s
    createWall(mapPosition + Vector3.new(101, 6, 0), Vector3.new(2, 12, 200)) -- Direita
    createWall(mapPosition + Vector3.new(-101, 6, 0), Vector3.new(2, 12, 200)) -- Esquerda
end

-- OpÃƒÂ§ÃƒÂ£o para gerar a ilha
Tab:AddButton({
    Name = "Gerar Ilha Sharingan/Kaimui",
    Callback = function()
        generateIsland()
    end
})

-- FunÃƒÂ§ÃƒÂ£o para teleportar para a ilha com efeito
local function teleportWithEffect()
    local character = player.Character or player.CharacterAdded:Wait()
    local root = character:FindFirstChild("HumanoidRootPart") or character:WaitForChild("HumanoidRootPart", 3)

    if not root then return end -- Impede erro caso o root nÃƒÂ£o seja encontrado

    local PlayerGui = player:FindFirstChildOfClass("PlayerGui")

    -- Criando a ScreenGui
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Parent = PlayerGui

    -- Criando a ImageLabel
    local ImageLabel = Instance.new("ImageLabel")
    ImageLabel.Parent = ScreenGui
    ImageLabel.Size = UDim2.new(0, 500, 0, 500)
    ImageLabel.Position = UDim2.new(0.5, -250, 0.42, -250)
    ImageLabel.Image = "rbxassetid://117307865668673"
    ImageLabel.BackgroundTransparency = 1

    -- Criando o Sound
    local Sound = Instance.new("Sound")
    Sound.Parent = PlayerGui
    Sound.SoundId = "rbxassetid://1592708450"
    Sound.Volume = 2
    Sound:Play()

    -- Efeito de rotaÃƒÂ§ÃƒÂ£o
    local RotationSpeed = -5
    local RotationConnection
    RotationConnection = RunService.RenderStepped:Connect(function(deltaTime)
        ImageLabel.Rotation = ImageLabel.Rotation + (RotationSpeed * (deltaTime * 60))
    end)

    -- Teleporta o jogador 2 segundos depois, enquanto a animaÃƒÂ§ÃƒÂ£o ocorre
    task.spawn(function()
        task.wait(2)
        if root then
            root.CFrame = CFrame.new(mapPosition + Vector3.new(0, 3, 0))
        end
    end)

    -- Espera 3 segundos antes do efeito de desaparecimento
    task.wait(3)

    -- Efeito de diminuiÃƒÂ§ÃƒÂ£o atÃƒÂ© sumir
    for i = 1, 30 do
        ImageLabel.Size = ImageLabel.Size - UDim2.new(0, 15, 0, 15)
        ImageLabel.Position = UDim2.new(0.5, -ImageLabel.Size.X.Offset / 2, 0.42, -ImageLabel.Size.Y.Offset / 2)
        task.wait(0.05)
    end

    -- Remover tudo
    RotationConnection:Disconnect()
    ScreenGui:Destroy()
    Sound:Destroy()
end

-- OpÃƒÂ§ÃƒÂ£o para teleportar para a ilha
Tab:AddButton({
    Name = "Teleportar para ilha Sharingan",
    Callback = function()
        teleportWithEffect()
    end
})


-- FunÃƒÂ§ÃƒÂ£o para teleportar para a ilha com efeito
local function teleportWithEffect()
    local character = player.Character or player.CharacterAdded:Wait()
    local root = character:FindFirstChild("HumanoidRootPart") or character:WaitForChild("HumanoidRootPart", 3)

    if not root then return end -- Impede erro caso o root nÃƒÂ£o seja encontrado

    local PlayerGui = player:FindFirstChildOfClass("PlayerGui")

    -- Criando a ScreenGui
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Parent = PlayerGui

    -- Criando a ImageLabel
    local ImageLabel = Instance.new("ImageLabel")
    ImageLabel.Parent = ScreenGui
    ImageLabel.Size = UDim2.new(0, 500, 0, 500)
    ImageLabel.Position = UDim2.new(0.5, -250, 0.42, -250)
    ImageLabel.Image = "rbxassetid://110470606429328"
    ImageLabel.BackgroundTransparency = 1

    -- Criando o Sound
    local Sound = Instance.new("Sound")
    Sound.Parent = PlayerGui
    Sound.SoundId = "rbxassetid://997057912"
    Sound.Volume = 2
    Sound:Play()

    -- Efeito de rotaÃƒÂ§ÃƒÂ£o
    local RotationSpeed = -5
    local RotationConnection
    RotationConnection = RunService.RenderStepped:Connect(function(deltaTime)
        ImageLabel.Rotation = Image
