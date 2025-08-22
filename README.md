--// Services
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

--// Main Gui
local screenGui = Instance.new("ScreenGui", playerGui)

-- MENU NHỎ
local menuSmall = Instance.new("ImageButton")
menuSmall.Size = UDim2.new(0, 60, 0, 60)
menuSmall.Position = UDim2.new(0.05, 0, 0.3, 0)
menuSmall.BackgroundTransparency = 1
menuSmall.Image = "http://www.roblox.com/asset/?id=13867262045"
menuSmall.Parent = screenGui
menuSmall.Active = true
menuSmall.Draggable = true

-- MENU LỚN
local menuBig = Instance.new("Frame")
menuBig.Size = UDim2.new(0, 0, 0, 0) -- ban đầu ẩn (animation mở)
menuBig.Position = UDim2.new(0.15, 0, 0.2, 0)
menuBig.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
menuBig.BorderSizePixel = 0
menuBig.Visible = false
menuBig.Parent = screenGui

local uiCornerMain = Instance.new("UICorner", menuBig)
uiCornerMain.CornerRadius = UDim.new(0, 12)

-- CREDIT
local credit = Instance.new("TextLabel", menuBig)
credit.Size = UDim2.new(1, 0, 0, 20)
credit.Position = UDim2.new(0, 0, 1, -20)
credit.BackgroundTransparency = 1
credit.Text = "YouTube : TheozVn"
credit.TextColor3 = Color3.fromRGB(200,200,200)
credit.TextScaled = true

-- Beachball Label
local ballLabel = Instance.new("TextLabel", menuBig)
ballLabel.Size = UDim2.new(0.9, 0, 0.1, 0)
ballLabel.Position = UDim2.new(0.05, 0, 0.05, 0)
ballLabel.BackgroundTransparency = 1
ballLabel.TextColor3 = Color3.new(1,1,1)
ballLabel.TextScaled = true
ballLabel.Text = "Beachballs: 0"

-- Button TP Dần
local btnSlow = Instance.new("TextButton", menuBig)
btnSlow.Size = UDim2.new(0.9, 0, 0.1, 0)
btnSlow.Position = UDim2.new(0.05, 0, 0.2, 0)
btnSlow.BackgroundColor3 = Color3.fromRGB(100, 100, 200)
btnSlow.Text = "TP Dần: OFF"
btnSlow.TextColor3 = Color3.new(1, 1, 1)
Instance.new("UICorner", btnSlow).CornerRadius = UDim.new(0, 8)

-- Button TP Thẳng
local btnFast = Instance.new("TextButton", menuBig)
btnFast.Size = UDim2.new(0.9, 0, 0.1, 0)
btnFast.Position = UDim2.new(0.05, 0, 0.35, 0)
btnFast.BackgroundColor3 = Color3.fromRGB(200, 100, 100)
btnFast.Text = "TP Thẳng: OFF"
btnFast.TextColor3 = Color3.new(1, 1, 1)
Instance.new("UICorner", btnFast).CornerRadius = UDim.new(0, 8)

-- Bảng màu
local colorPicker = Instance.new("ImageButton", menuBig)
colorPicker.Size = UDim2.new(0.9, 0, 0.2, 0)
colorPicker.Position = UDim2.new(0.05, 0, 0.5, 0)
colorPicker.Image = "rbxassetid://7024960431"
colorPicker.BackgroundColor3 = Color3.fromRGB(30,30,30)

-- Thông báo
local warnLabel = Instance.new("TextLabel", screenGui)
warnLabel.Size = UDim2.new(0.4, 0, 0.1, 0)
warnLabel.Position = UDim2.new(0.3, 0, 0.05, 0)
warnLabel.BackgroundTransparency = 0.3
warnLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
warnLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
warnLabel.TextScaled = true
warnLabel.Visible = false
Instance.new("UICorner", warnLabel).CornerRadius = UDim.new(0, 10)

-- Trạng thái
local runningSlow, runningFast = false, false

-- Lấy Beachballs gần nhất
local function getBeachballsSorted()
	local balls = {}
	if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
		for _, obj in pairs(workspace:GetDescendants()) do
			if obj:IsA("Part") and obj.Name == "Beachball" then
				local dist = (obj.Position - player.Character.HumanoidRootPart.Position).Magnitude
				table.insert(balls, {part=obj, dist=dist})
			end
		end
		table.sort(balls, function(a, b) return a.dist < b.dist end)
	end
	ballLabel.Text = "Beachballs: " .. tostring(#balls)
	return balls
end

-- TP Dần
local function tpSlow()
	while runningSlow do
		local balls = getBeachballsSorted()
		for _, ball in ipairs(balls) do
			if not runningSlow then break end
			if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
				local hrp = player.Character.HumanoidRootPart
				local tween = TweenService:Create(
					hrp,
					TweenInfo.new(0.4, Enum.EasingStyle.Linear),
					{CFrame = CFrame.new(ball.part.Position + Vector3.new(0, 2, 0))}
				)
				tween:Play()
				tween.Completed:Wait()
			end
		end
		task.wait(0.5)
	end
end

-- TP Thẳng
local function tpFast()
	while runningFast do
		local balls = getBeachballsSorted()
		for _, ball in ipairs(balls) do
			if not runningFast then break end
			if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
				local hrp = player.Character.HumanoidRootPart
				hrp.CFrame = CFrame.new(ball.part.Position + Vector3.new(0, 2, 0))
				task.wait(0.15)
			end
		end
		task.wait(0.5)
	end
end

-- Toggle TP Dần
btnSlow.MouseButton1Click:Connect(function()
	if runningFast then
		warnLabel.Text = "Chỉ được bật 1 chế độ thôi!"
		warnLabel.Visible = true
		task.delay(3, function() warnLabel.Visible = false end)
		return
	end
	runningSlow = not runningSlow
	btnSlow.Text = runningSlow and "TP Dần: ON" or "TP Dần: OFF"
	if runningSlow then task.spawn(tpSlow) end
end)

-- Toggle TP Thẳng
btnFast.MouseButton1Click:Connect(function()
	if runningSlow then
		warnLabel.Text = "Chỉ được bật 1 chế độ thôi!"
		warnLabel.Visible = true
		task.delay(3, function() warnLabel.Visible = false end)
		return
	end
	runningFast = not runningFast
	btnFast.Text = runningFast and "TP Thẳng: ON" or "TP Thẳng: OFF"
	if runningFast then task.spawn(tpFast) end
end)

-- Đổi màu
colorPicker.MouseButton1Click:Connect(function(x,y)
	local relX = (x - colorPicker.AbsolutePosition.X) / colorPicker.AbsoluteSize.X
	local relY = (y - colorPicker.AbsolutePosition.Y) / colorPicker.AbsoluteSize.Y
	local chosenColor = Color3.fromHSV(relX, 1, 1 - relY)
	menuBig.BackgroundColor3 = chosenColor
end)

-- Animation mở Menu Lớn
local opened = false
menuSmall.MouseButton1Click:Connect(function()
	opened = not opened
	if opened then
		menuBig.Visible = true
		TweenService:Create(menuBig, TweenInfo.new(0.4, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size=UDim2.new(0, 300, 0, 350)}):Play()
	else
		TweenService:Create(menuBig, TweenInfo.new(0.4, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {Size=UDim2.new(0, 0, 0, 0)}):Play()
		task.delay(0.4, function()
			if not opened then menuBig.Visible = false end
		end)
	end
end)
