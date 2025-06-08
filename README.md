-- GHOST PARK Hub por Iguatu (Fly + Fling com estilo preto e roxo)

-- GUI
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "GHOSTPARK_Hub"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 300, 0, 200)
frame.Position = UDim2.new(0, 50, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
frame.BorderSizePixel = 0

-- Título
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.Position = UDim2.new(0, 0, 0, 0)
title.Text = "👻 GHOST PARK"
title.TextColor3 = Color3.fromRGB(180, 0, 255)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBlack
title.TextSize = 20

-- Botão Fly
local flyBtn = Instance.new("TextButton", frame)
flyBtn.Size = UDim2.new(0, 120, 0, 40)
flyBtn.Position = UDim2.new(0, 20, 0, 50)
flyBtn.Text = "🕊️ Ativar Fly"
flyBtn.BackgroundColor3 = Color3.fromRGB(60, 0, 100)
flyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
flyBtn.Font = Enum.Font.Gotham
flyBtn.TextSize = 16

-- Campo Nome do Jogador
local nomeBox = Instance.new("TextBox", frame)
nomeBox.Size = UDim2.new(0, 180, 0, 35)
nomeBox.Position = UDim2.new(0, 20, 0, 110)
nomeBox.PlaceholderText = "Digite o nome do jogador"
nomeBox.BackgroundColor3 = Color3.fromRGB(40, 0, 60)
nomeBox.TextColor3 = Color3.new(1,1,1)
nomeBox.Font = Enum.Font.Gotham
nomeBox.TextSize = 14

-- Botão Fling
local flingBtn = Instance.new("TextButton", frame)
flingBtn.Size = UDim2.new(0, 80, 0, 35)
flingBtn.Position = UDim2.new(0, 210, 0, 110)
flingBtn.Text = "🔄 Fling"
flingBtn.BackgroundColor3 = Color3.fromRGB(120, 0, 200)
flingBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
flingBtn.Font = Enum.Font.GothamBold
flingBtn.TextSize = 14

-- Função de Fly
local function startFly()
	local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
	local hrp = char:WaitForChild("HumanoidRootPart")
	local bv = Instance.new("BodyVelocity", hrp)
	bv.MaxForce = Vector3.new(1,1,1) * math.huge
	bv.Velocity = Vector3.zero

	local UIS = game:GetService("UserInputService")
	local dir = Vector3.zero
	local speed = 60
	local flying = true

	UIS.InputBegan:Connect(function(i)
		if i.KeyCode == Enum.KeyCode.W then dir = Vector3.new(0,0,-1) end
		if i.KeyCode == Enum.KeyCode.S then dir = Vector3.new(0,0,1) end
		if i.KeyCode == Enum.KeyCode.A then dir = Vector3.new(-1,0,0) end
		if i.KeyCode == Enum.KeyCode.D then dir = Vector3.new(1,0,0) end
	end)

	UIS.InputEnded:Connect(function(_) dir = Vector3.zero end)

	coroutine.wrap(function()
		while flying and bv.Parent do
			task.wait()
			bv.Velocity = (workspace.CurrentCamera.CFrame:VectorToWorldSpace(dir)) * speed
		end
	end)()
end

flyBtn.MouseButton1Click:Connect(function()
	startFly()
end)

-- Função de Fling
local function flingPlayerByName(name)
	local target = Players:FindFirstChild(name)
	if not target or not target.Character then return warn("Jogador não encontrado.") end

	local hrp = LocalPlayer.Character:WaitForChild("HumanoidRootPart")
	local targetHrp = target.Character:WaitForChild("HumanoidRootPart")

	local bp = Instance.new("BodyPosition", hrp)
	bp.MaxForce = Vector3.new(1,1,1) * math.huge
	bp.Position = targetHrp.Position

	local bg = Instance.new("BodyGyro", hrp)
	bg.MaxTorque = Vector3.new(1,1,1) * math.huge
	bg.CFrame = hrp.CFrame

	coroutine.wrap(function()
		while task.wait() do
			if not target or not target.Character or not target.Character:FindFirstChild("HumanoidRootPart") then break end
			bp.Position = target.Character.HumanoidRootPart.Position
			hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(1000), 0)
		end
	end)()
end

flingBtn.MouseButton1Click:Connect(function()
	local nome = nomeBox.Text
	if nome and nome ~= "" then
		flingPlayerByName(nome)
	end
end)
