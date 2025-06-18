local UserInputService = game:GetService("UserInputService")
local plr = game.Players.LocalPlayer

-- GUI Principal
local guiyy = Instance.new("ScreenGui")
guiyy.Name = "WallHopGui"
guiyy.ResetOnSpawn = false
guiyy.Parent = game.CoreGui

-- Estado
local toggle = false
local InfiniteJumpEnabled = true

-- Painel e botão
local menu = Instance.new("Frame")
menu.Size = UDim2.new(0.125, 0, 0.075, 0)
menu.Position = UDim2.new(0.01, 0, 0.01, 0)
menu.BackgroundTransparency = 0.85
menu.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
menu.Parent = guiyy
Instance.new("UICorner", menu)

local button = Instance.new("TextButton", menu)
button.Size = UDim2.new(0.82, 0, 0.65, 0)
button.Position = UDim2.new(0.5, 0, 0.5, 0)
button.AnchorPoint = Vector2.new(0.5, 0.5)
button.BackgroundTransparency = 0.85
button.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
button.Text = "Walking"
button.TextScaled = true
button.Font = Enum.Font.Gotham
button.TextColor3 = Color3.fromRGB(0, 0, 0)
button.BorderSizePixel = 0
Instance.new("UICorner", button)

-- Drag para mover o painel
local dragging, dragInput, dragStart, startPos
local function update(input)
	local delta = input.Position - dragStart
	menu.Position = UDim2.new(startPos.X.Scale,
		startPos.X.Offset + delta.X,
		startPos.Y.Scale,
		startPos.Y.Offset + delta.Y)
end

menu.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1
	or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = menu.Position
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

menu.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement
	or input.UserInputType == Enum.UserInputType.Touch then
		dragInput = input
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if dragging and input == dragInput then
		update(input)
	end
end)

-- Alterna Walking/WallHop
button.MouseButton1Click:Connect(function()
	toggle = not toggle
	button.Text = toggle and "WallHop" or "Walking"
end)

-- Função de rotação suave (ajustada)
local function smoothRotate(angle, steps, delayTime)
	local char = plr.Character
	if not char then return end
	local root = char:FindFirstChild("HumanoidRootPart")
	if not root then return end

	local stepAngle = angle / steps
	for _ = 1, steps do
		root.CFrame = root.CFrame * CFrame.Angles(0, stepAngle, 0)
		task.wait(delayTime)
	end
end

-- WallHop com rotação rápida (ajustada)
UserInputService.JumpRequest:Connect(function()
	if toggle and InfiniteJumpEnabled then
		local char = plr.Character
		if not char then return end
		local humanoid = char:FindFirstChildOfClass("Humanoid")
		local root = char:FindFirstChild("HumanoidRootPart")
		if humanoid and root then
			humanoid:ChangeState("Jumping")
			InfiniteJumpEnabled = false

			-- Giro rápido com ângulo de ~68° para cada lado
			smoothRotate(-1.2, 3, 0.012) -- mais rápido
			task.wait(0.055) -- pausa menor
			smoothRotate(1.2, 3, 0.012)

			InfiniteJumpEnabled = true
		end
	end
end)
