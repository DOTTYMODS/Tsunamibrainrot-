--==================================================
--  DOTTYMODS - SISTEMA RESTAURADO + JUMPS (FIXED)
--==================================================

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer

--==================================================
-- POSICIONES (SISTEMA NEIDERHUB ORIGINAL - NO TOCAR)
--==================================================
local basePositions = {
	Vector3.new(152.51, 5.38, -137),
	Vector3.new(247.72, 5.38, -137),
	Vector3.new(343.75, 5.38, -137),
	Vector3.new(483.16, 5.38, -137),
	Vector3.new(655.36, 5.38, -137),
	Vector3.new(898.47, 5.38, -137),
	Vector3.new(1343.44, 5.38, -137),
	Vector3.new(1928.01, 5.38, -137),
	Vector3.new(2618.69, 5.38, -137),
	Vector3.new(3133.93, 5.38, -137),
	Vector3.new(3485.94, 5.38, -137),
	Vector3.new(3841.31, 5.38, -137),
}

local LAST_INDEX = #basePositions
local SAFE_Y = 10
local currentSpeed = 100
local infJumpActive = false
local modifiedParts = {}
local collisionActive = false

--==================================================
-- UI PRINCIPAL (ESTILO RAINBOW)
--==================================================
local ScreenGui = Instance.new("ScreenGui", player.PlayerGui)
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 220, 0, 380) 
Frame.Position = UDim2.new(0.5, -110, 0.5, -190)
Frame.BackgroundColor3 = Color3.fromRGB(15,15,15)
Frame.Active = true
Frame.Draggable = true
Instance.new("UICorner", Frame).CornerRadius = UDim.new(0,12)

local Stroke = Instance.new("UIStroke", Frame)
Stroke.Thickness = 2

local Title = Instance.new("TextLabel", Frame)
Title.Size = UDim2.new(1, 0, 0, 45)
Title.BackgroundTransparency = 1
Title.Text = "DOTTYMODS"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20
Title.ZIndex = 100

RunService.RenderStepped:Connect(function()
	local color = Color3.fromHSV(tick() % 5 / 5, 1, 1)
	Stroke.Color = color
	Title.TextColor3 = color
end)

local MinBtn = Instance.new("TextButton", Frame)
MinBtn.Size = UDim2.new(0,30,0,22)
MinBtn.Position = UDim2.new(1,-35,0,5)
MinBtn.Text = "—"
MinBtn.Font = Enum.Font.GothamBold
MinBtn.BackgroundColor3 = Color3.fromRGB(30,30,30)
MinBtn.TextColor3 = Color3.new(1,1,1)
MinBtn.ZIndex = 101
Instance.new("UICorner", MinBtn).CornerRadius = UDim.new(0,6)

--==================================================
-- LÓGICA DE BOTONES (NO TOCAR TP/IR/VIP)
--==================================================
local function createButton(text, y)
	local b = Instance.new("TextButton", Frame)
	b.Size = UDim2.new(0.85, 0, 0, 32)
	b.Position = UDim2.new(0.075, 0, 0, y)
	b.Text = text
	b.Font = Enum.Font.GothamBold
	b.TextSize = 13
	b.TextColor3 = Color3.new(1,1,1)
	b.BackgroundColor3 = Color3.fromRGB(30,30,30)
	b.ZIndex = 50
	Instance.new("UICorner", b).CornerRadius = UDim.new(0,8)
	local s = Instance.new("UIStroke", b)
	RunService.RenderStepped:Connect(function()
		s.Color = Color3.fromHSV(tick() % 5 / 5, 0.8, 0.8)
	end)
	return b
end

local TpButton    = createButton("TP", 50)
local IrButton    = createButton("IR", 90)
local VipButton   = createButton("Eliminar VIP", 130)
local InfJumpBtn  = createButton("Infinite Jump: OFF", 170)

local JumpInput = Instance.new("TextBox", Frame)
JumpInput.Size = UDim2.new(0.4, 0, 0, 32)
JumpInput.Position = UDim2.new(0.075, 0, 0, 210)
JumpInput.PlaceholderText = "Metros"
JumpInput.Text = ""
JumpInput.Font = Enum.Font.GothamBold
JumpInput.TextSize = 13
JumpInput.BackgroundColor3 = Color3.fromRGB(30,30,30)
JumpInput.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", JumpInput)

local SuperJumpBtn = Instance.new("TextButton", Frame)
SuperJumpBtn.Size = UDim2.new(0.4, 0, 0, 32)
SuperJumpBtn.Position = UDim2.new(0.525, 0, 0, 210)
SuperJumpBtn.Text = "Set Jump"
SuperJumpBtn.Font = Enum.Font.GothamBold
SuperJumpBtn.TextSize = 13
SuperJumpBtn.BackgroundColor3 = Color3.fromRGB(40,40,40)
SuperJumpBtn.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", SuperJumpBtn)

--==================================================
-- SLIDER DE VELOCIDAD
--==================================================
local SliderFrame = Instance.new("Frame", Frame)
SliderFrame.Size = UDim2.new(0.85, 0, 0, 4)
SliderFrame.Position = UDim2.new(0.075, 0, 0, 270)
SliderFrame.BackgroundColor3 = Color3.fromRGB(50,50,50)
SliderFrame.BorderSizePixel = 0

local SliderDot = Instance.new("TextButton", SliderFrame)
SliderDot.Size = UDim2.new(0, 16, 0, 16)
SliderDot.Position = UDim2.new(0.1, -8, 0.5, -8)
SliderDot.BackgroundColor3 = Color3.new(1,1,1)
SliderDot.Text = ""
Instance.new("UICorner", SliderDot).CornerRadius = UDim.new(1,0)

local SpeedText = Instance.new("TextLabel", Frame)
SpeedText.Size = UDim2.new(1, 0, 0, 20)
SpeedText.Position = UDim2.new(0, 0, 0, 285)
SpeedText.BackgroundTransparency = 1
SpeedText.Text = "Velocidad: 100"
SpeedText.Font = Enum.Font.GothamBold
SpeedText.TextSize = 12
SpeedText.TextColor3 = Color3.new(1,1,1)

local dragging = false
SliderDot.MouseButton1Down:Connect(function() dragging = true end)
UserInputService.InputEnded:Connect(function(input) 
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then dragging = false end 
end)

RunService.RenderStepped:Connect(function()
	if dragging then
		local mousePos = UserInputService:GetMouseLocation().X
		local relativePos = math.clamp((mousePos - SliderFrame.AbsolutePosition.X) / SliderFrame.AbsoluteSize.X, 0, 1)
		SliderDot.Position = UDim2.new(relativePos, -8, 0.5, -8)
		currentSpeed = math.floor(50 + (relativePos * 950))
		SpeedText.Text = "Velocidad: " .. currentSpeed
	end
end)

--==================================================
-- SISTEMA DE TP (LÓGICA NEIDERHUB - BLOQUEADO)
--==================================================
local function normalizeY(hrp)
	if hrp.Position.Y < SAFE_Y then
		hrp.CFrame = CFrame.new(hrp.Position.X, SAFE_Y, hrp.Position.Z)
	end
end

local function disableCollision(hrp)
	if collisionActive then return end
	collisionActive = true
	modifiedParts = {}
	for _, obj in ipairs(workspace:GetDescendants()) do
		if obj:IsA("BasePart") and obj.Position.Y > hrp.Position.Y - 5 then
			modifiedParts[obj] = obj.CanCollide
			obj.CanCollide = false
		end
	end
end

local function restoreCollision()
	if not collisionActive then return end
	for p, v in pairs(modifiedParts) do if p and p.Parent then p.CanCollide = v end end
	modifiedParts = {}
	collisionActive = false
end

local function nearestPos(hrp)
	local bestIndex = 1
	local bestDist = (hrp.Position - basePositions[1]).Magnitude
	for i, p in ipairs(basePositions) do
		local d = (hrp.Position - p).Magnitude
		if d < bestDist then
			bestDist = d
			bestIndex = i
		end
	end
	return basePositions[bestIndex]
end

local function tweenTo(hrp, pos)
	local t = (hrp.Position - pos).Magnitude / currentSpeed
	local tw = TweenService:Create(hrp, TweenInfo.new(t, Enum.EasingStyle.Linear), {CFrame = CFrame.new(pos)})
	tw:Play()
	tw.Completed:Wait()
end

TpButton.MouseButton1Click:Connect(function()
	local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	normalizeY(hrp)
	disableCollision(hrp)
	tweenTo(hrp, nearestPos(hrp))
	task.wait(0.1)
	tweenTo(hrp, basePositions[1])
	restoreCollision()
end)

IrButton.MouseButton1Click:Connect(function()
	local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	normalizeY(hrp)
	disableCollision(hrp)
	tweenTo(hrp, nearestPos(hrp))
	task.wait(0.1)
	tweenTo(hrp, basePositions[LAST_INDEX])
	restoreCollision()
end)

VipButton.MouseButton1Click:Connect(function()
	for _, obj in ipairs(workspace:GetDescendants()) do
		if obj:IsA("BasePart") and (obj.Name:upper() == "VIP" or obj.Name:upper() == "VIP_PLUS") then
			obj:Destroy()
		end
	end
end)

--==================================================
-- LÓGICA DE SALTO (REPARADA)
--==================================================
UserInputService.JumpRequest:Connect(function()
	if infJumpActive then
		local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
		if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
	end
end)

InfJumpBtn.MouseButton1Click:Connect(function()
	infJumpActive = not infJumpActive
	InfJumpBtn.Text = infJumpActive and "Infinite Jump: ON" or "Infinite Jump: OFF"
	InfJumpBtn.TextColor3 = infJumpActive and Color3.fromRGB(0, 255, 150) or Color3.new(1,1,1)
end)

-- SOLUCIÓN AL SUPER JUMP:
SuperJumpBtn.MouseButton1Click:Connect(function()
	local char = player.Character
	local hum = char and char:FindFirstChildOfClass("Humanoid")
	local metros = tonumber(JumpInput.Text)
	
	if hum and metros then
		-- Forzamos que el juego acepte el nuevo poder de salto
		hum.UseJumpPower = true 
		hum.JumpPower = metros
		-- Por si el juego usa JumpHeight:
		hum.JumpHeight = metros 
		
		SuperJumpBtn.Text = "¡Listo!"
		SuperJumpBtn.TextColor3 = Color3.fromRGB(0, 255, 150)
		task.wait(1)
		SuperJumpBtn.Text = "Set Jump"
		SuperJumpBtn.TextColor3 = Color3.new(1,1,1)
	end
end)

-- MINIMIZAR
local buttons = {TpButton, IrButton, VipButton, InfJumpBtn, SuperJumpBtn, JumpInput}
local minimized = false
MinBtn.MouseButton1Click:Connect(function()
	minimized = not minimized
	for _, b in ipairs(buttons) do b.Visible = not minimized end
	Title.Visible = not minimized
	SliderFrame.Visible = not minimized
	SpeedText.Visible = not minimized
	MinBtn.Text = minimized and "+" or "—"
	Frame.Size = minimized and UDim2.new(0,220,0,40) or UDim2.new(0, 220, 0, 380)
end)
