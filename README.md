-- Referência ao PlayerGui do jogador local
local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Criação do ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MenuGui"
screenGui.Parent = playerGui

-- Botão de abrir/fechar (rosa choque)
local openButton = Instance.new("TextButton")
openButton.Name = "OpenButton"
openButton.Size = UDim2.new(0, 80, 0, 40)
openButton.Position = UDim2.new(0, 10, 0, 10)
openButton.BackgroundColor3 = Color3.fromRGB(44, 44, 44) -- Rosa choque
openButton.Text = "Menu"
openButton.TextColor3 = Color3.fromRGB(255, 255, 255)
openButton.TextSize = 18
openButton.Font = Enum.Font.Gotham
openButton.Parent = screenGui

local openButtonCorner = Instance.new("UICorner")
openButtonCorner.CornerRadius = UDim.new(0, 8)
openButtonCorner.Parent = openButton

-- Frame do menu (cinza escuro)
local menuFrame = Instance.new("Frame")
menuFrame.Name = "MenuFrame"
menuFrame.Size = UDim2.new(0, 300, 0, 260)
menuFrame.Position = UDim2.new(0.5, -150, 0.5, -130)
menuFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 50) -- Cinza escuro
menuFrame.BorderSizePixel = 0
menuFrame.Visible = false
menuFrame.Parent = screenGui

local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0, 8)
uiCorner.Parent = menuFrame

-- Função para criar botões
local function createButton(name, text, posX, posY, callback)
	local button = Instance.new("TextButton")
	button.Name = name
	button.Size = UDim2.new(0, 100, 0, 40)
	button.Position = UDim2.new(0, posX, 0, posY)
	button.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
	button.Text = text
	button.TextColor3 = Color3.fromRGB(255, 255, 255)
	button.TextSize = 16
	button.Font = Enum.Font.Gotham
	button.Parent = menuFrame

	local buttonCorner = Instance.new("UICorner")
	buttonCorner.CornerRadius = UDim.new(0, 5)
	buttonCorner.Parent = button

	button.MouseButton1Click:Connect(callback)
end

-- Funções para mudar o horário e clima
local lighting = game:GetService("Lighting")
local weatherActive = false

-- Limpa gotas e flocos existentes
local function clearWeather()
	weatherActive = false
	for _, obj in pairs(workspace:GetDescendants()) do
		if obj:IsA("Part") and (obj.Name == "RainDrop" or obj.Name == "SnowFlake") then
			obj:Destroy()
		end
	end
end

local function setDay()
	clearWeather()
	lighting.ClockTime = 12
	lighting.Brightness = 2
	print("Dia")
end

local function setNight()
	clearWeather()
	lighting.ClockTime = 0
	lighting.Brightness = 0.5
	print("Noite")
end

local function setMorning()
	clearWeather()
	lighting.ClockTime = 6
	lighting.Brightness = 1.5
	print("Manhã")
end

local function setAfternoon()
	clearWeather()
	lighting.ClockTime = 15
	lighting.Brightness = 1.8
	print("Tarde")
end

local function setRain()
	clearWeather()
	lighting.ClockTime = 14
	lighting.Brightness = 0.8

	weatherActive = true
	spawn(function()
		while weatherActive do
			local drop = Instance.new("Part")
			drop.Name = "RainDrop"
			drop.Size = Vector3.new(0.1, 0.5, 0.1)
			drop.Shape = Enum.PartType.Cylinder
			drop.BrickColor = BrickColor.new("Bright blue")
			drop.Position = Vector3.new(
				math.random(-50, 50),
				50,
				math.random(-50, 50)
			)
			drop.Anchored = false
			drop.CanCollide = true
			drop.Parent = workspace

			spawn(function()
				wait(5)
				if drop and drop.Parent then
					drop:Destroy()
				end
			end)
			wait(0.05)
		end
	end)
	print("Chover")
end

local function setSnow()
	clearWeather()
	lighting.ClockTime = 10
	lighting.Brightness = 1

	weatherActive = true
	spawn(function()
		while weatherActive do
			local flake = Instance.new("Part")
			flake.Name = "SnowFlake"
			flake.Size = Vector3.new(0.3, 0.3, 0.3)
			flake.Shape = Enum.PartType.Ball
			flake.BrickColor = BrickColor.new("White")
			flake.Position = Vector3.new(
				math.random(-50, 50),
				50,
				math.random(-50, 50)
			)
			flake.Anchored = false
			flake.CanCollide = true
			flake.Parent = workspace

			local bodyVelocity = Instance.new("BodyVelocity")
			bodyVelocity.Velocity = Vector3.new(0, -5, 0)
			bodyVelocity.MaxForce = Vector3.new(1000, 1000, 1000)
			bodyVelocity.Parent = flake

			spawn(function()
				wait(5)
				if flake and flake.Parent then
					flake:Destroy()
				end
			end)
			wait(0.1)
		end
	end)
	print("Nevar")
end

-- Criando os botões no menu
createButton("DiaButton", "Dia", 20, 20, setDay)
createButton("NoiteButton", "Noite", 180, 20, setNight)
createButton("ManhaButton", "Manhã", 20, 80, setMorning)
createButton("TardeButton", "Tarde", 180, 80, setAfternoon)
createButton("NevarButton", "Nevar", 20, 140, setSnow)
createButton("ChoverButton", "Chover", 180, 140, setRain)

-- Lógica para abrir/fechar o menu
local isOpen = false
openButton.MouseButton1Click:Connect(function()
	isOpen = not isOpen
	menuFrame.Visible = isOpen
	openButton.Text = isOpen and "Fechar" or "Menu"
end)
