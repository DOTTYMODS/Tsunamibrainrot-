--==================================================
--  DOTTYMODS RAINBOW + INSTANT INTERACT
--==================================================

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local ProximityPromptService = game:GetService("ProximityPromptService")

local player = Players.LocalPlayer

--==================================================
-- POSICIONES
--==================================================

local basePositions = {
	Vector3.new(152.51, 8, -137),
	Vector3.new(243.13, 8, -137),
	Vector3.new(333.11, 8, -137),
	Vector3.new(470.77, 8, -137),
	Vector3.new(666.46, 8, -137),
	Vector3.new(931.37, 8, -137),
	Vector3.new(1322.80, 8, -137),
	Vector3.new(1915.23, 8, -137),
	Vector3.new(2431.64, 8, -137),
}

local LAST_INDEX = #basePositions

--==================================================
-- UI PRINCIPAL DOTTYMODS
--==================================================

local ScreenGui = Instance.new("ScreenGui", player.PlayerGui)
ScreenGui.ResetOnSpawn = false

local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 240, 0, 310) -- Aumentado un poco para el nuevo botón
Frame.Position = UDim2.new(0.5, -120, 0.5, -155)
Frame.BackgroundColor3 = Color3.fromRGB(15,15,15)
Frame.Active = true
Frame.Draggable = true
Instance.new("UICorner", Frame).CornerRadius = UDim.new(0,10)

local Stroke = Instance.new("UIStroke", Frame)
Stroke.Thickness = 2

local Title = Instance.new("TextLabel", Frame)
Title.Size = UDim2.new(1,-40,0,40)
Title.BackgroundTransparency = 1
Title.Text = "DOTTYMODS"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20

-- EFECTO RAINBOW GLOBAL
RunService.RenderStepped:Connect(function()
	local hue = tick() % 5 / 5
	local color = Color3.fromHSV(hue, 1, 1)
	Stroke.Color = color
	Title.TextColor3 = color
end)

local MinBtn = Instance.new("TextButton", Frame)
MinBtn.Size = UDim2.new(0,30,0,22)
MinBtn.Position = UDim2.new(1,-34,0,4)
MinBtn.Text = "—"
MinBtn.Font = Enum.Font.GothamBold
MinBtn.TextSize = 18
MinBtn.TextColor3 = Color3.new(1,1,1)
MinBtn.BackgroundColor3 = Color3.fromRGB(30,30,30)
Instance.new("UICorner", MinBtn).CornerRadius = UDim.new(0,6)

--==================================================
-- BOTONES LÓGICA
--==================================================

local function createButton(text, yScale, parent)
	local b = Instance.new("TextButton", parent)
	b.Size = UDim2.new(0.85,0,0,32)
	b.Position = UDim2.new(0.075,0,yScale,0)
	b.Text = text
	b.Font = Enum.Font.GothamBold
	b.TextSize = 13
	b.TextColor3 = Color3.new(1,1,1)
	b.BackgroundColor3 = Color3.fromRGB(30,30,30)
	Instance.new("UICorner", b).CornerRadius = UDim.new(0,8)
	local s = Instance.new("UIStroke", b)
	s.Thickness = 1
    
    RunService.RenderStepped:Connect(function()
        s.Color = Color3.fromHSV(tick() % 5 / 5, 0.8, 0.8)
    end)
	return b
end

local TpButton      = createButton("TP", 0.15, Frame)
local IrButton      = createButton("IR", 0.27, Frame)
local VipButton     = createButton("Eliminar VIP", 0.39, Frame)
local ParkButton    = createButton("Easy Parkour", 0.51, Frame)
local MoneyButton   = createButton("TP Money", 0.63, Frame)
local InteractBtn   = createButton("Instant Interact", 0.75, Frame)

local buttons = {TpButton, IrButton, VipButton, ParkButton, MoneyButton, InteractBtn}

local Credit = Instance.new("TextLabel", Frame)
Credit.Size = UDim2.new(1,0,0,22)
Credit.Position = UDim2.new(0,0,1,-22)
Credit.BackgroundTransparency = 1
Credit.Text = "DOTTYMODS™"
Credit.Font = Enum.Font.GothamBold
Credit.TextSize = 12
Credit.TextColor3 = Color3.fromRGB(200,200,200)

--==================================================
-- FUNCIÓN INSTANT INTERACT (DIXON HUB)
--==================================================

local function Instantaneo(prompt)
    prompt.HoldDuration = 0
    prompt.ClickablePrompt = true
end

InteractBtn.MouseButton1Click:Connect(function()
    print("DOTTYMODS: Modo Instant Interact Activado")
    InteractBtn.Text = "Interact: ACTIVADO"
    InteractBtn.TextColor3 = Color3.fromRGB(0, 255, 150)
    
    for _, prompt in pairs(game:GetDescendants()) do
        if prompt:IsA("ProximityPrompt") then
            Instantaneo(prompt)
        end
    end
    
    ProximityPromptService.PromptAdded:Connect(function(prompt)
        Instantaneo(prompt)
    end)
end)

--==================================================
-- MENÚ TP MONEY (RAINBOW + MINIMIZABLE)
--==================================================

local moneyFrame
local moneyMinimized = false
local moneyButtons = {}

MoneyButton.MouseButton1Click:Connect(function()
	if moneyFrame then 
		moneyFrame.Visible = not moneyFrame.Visible 
		return 
	end

	moneyFrame = Instance.new("Frame", ScreenGui)
	moneyFrame.Size = UDim2.new(0, 220, 0, 190)
	moneyFrame.Position = UDim2.new(0.1, 0, 0.4, 0)
	moneyFrame.BackgroundColor3 = Color3.fromRGB(15,15,15)
	moneyFrame.Active = true
	moneyFrame.Draggable = true
	Instance.new("UICorner", moneyFrame).CornerRadius = UDim.new(0,10)

	local mStroke = Instance.new("UIStroke", moneyFrame)
	mStroke.Thickness = 2

	local mTitle = Instance.new("TextLabel", moneyFrame)
	mTitle.Size = UDim2.new(1,-40,0,40)
	mTitle.BackgroundTransparency = 1
	mTitle.Text = "MONEY TP"
	mTitle.Font = Enum.Font.GothamBold
	mTitle.TextSize = 16

	RunService.RenderStepped:Connect(function()
		local c = Color3.fromHSV(tick() % 5 / 5, 1, 1)
		mStroke.Color = c
		mTitle.TextColor3 = c
	end)

	local mMinBtn = Instance.new("TextButton", moneyFrame)
	mMinBtn.Size = UDim2.new(0,30,0,22)
	mMinBtn.Position = UDim2.new(1,-34,0,4)
	mMinBtn.Text = "—"
	mMinBtn.Font = Enum.Font.GothamBold
	mMinBtn.BackgroundColor3 = Color3.fromRGB(30,30,30)
	mMinBtn.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", mMinBtn).CornerRadius = UDim.new(0,6)

	local function tpAction(partName)
		local hrp = player.Character:WaitForChild("HumanoidRootPart")
		local part = workspace:FindFirstChild(partName, true)
		if part then hrp.CFrame = part.CFrame + Vector3.new(0,3,0) end
	end

	local b1 = createButton("TP Money 1", 0.25, moneyFrame)
	local b2 = createButton("TP Money 2", 0.48, moneyFrame)
	local b3 = createButton("TP Money 3", 0.71, moneyFrame)
	
	b1.MouseButton1Click:Connect(function() tpAction("MoneyObby1End") end)
	b2.MouseButton1Click:Connect(function() tpAction("MoneyObby2End") end)
	b3.MouseButton1Click:Connect(function() tpAction("MoneyObby3End") end)

	moneyButtons = {b1, b2, b3}

	mMinBtn.MouseButton1Click:Connect(function()
		moneyMinimized = not moneyMinimized
		for _, b in ipairs(moneyButtons) do b.Visible = not moneyMinimized end
		mMinBtn.Text = moneyMinimized and "+" or "—"
		moneyFrame.Size = moneyMinimized and UDim2.new(0,220,0,40) or UDim2.new(0,220,0,190)
	end)
end)

--==================================================
-- LÓGICA DE TELEPORTE / FUNCIONES RESTANTES
--==================================================

local collisionActive = false
local modifiedParts = {}

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

local function tweenTo(hrp, pos)
	local t = (hrp.Position - pos).Magnitude / 300
	local tw = TweenService:Create(hrp, TweenInfo.new(t, Enum.EasingStyle.Linear), {CFrame = CFrame.new(pos)})
	tw:Play()
	tw.Completed:Wait()
end

TpButton.MouseButton1Click:Connect(function()
	local hrp = player.Character.HumanoidRootPart
	disableCollision(hrp)
	tweenTo(hrp, basePositions[1])
	restoreCollision()
end)

IrButton.MouseButton1Click:Connect(function()
	local hrp = player.Character.HumanoidRootPart
	disableCollision(hrp)
	tweenTo(hrp, basePositions[LAST_INDEX])
	restoreCollision()
end)

VipButton.MouseButton1Click:Connect(function()
	for _, o in ipairs(workspace:GetDescendants()) do
		if o:IsA("BasePart") and (o.Name:upper() == "VIP" or o.Name:upper() == "VIP_PLUS") then o:Destroy() end
	end
end)

ParkButton.MouseButton1Click:Connect(function()
	for _, o in ipairs(workspace:GetDescendants()) do
		if o:IsA("BasePart") and (o.Name:upper() == "LAVA" or o.Name:upper() == "LAVA2") then o:Destroy() end
	end
end)

MinBtn.MouseButton1Click:Connect(function()
	minimized = not minimized
	for _, b in ipairs(buttons) do b.Visible = not minimized end
	Credit.Visible = not minimized
	MinBtn.Text = minimized and "+" or "—"
	Frame.Size = minimized and UDim2.new(0,240,0,40) or UDim2.new(0,240,0,310)
end)
