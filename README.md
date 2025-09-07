-- LocalScript: Kill HUD - Fly + Kill Aura (English)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")

-- Settings
local flySpeed = 50
local attackRange = 10
local flyEnabled = false
local auraEnabled = false

-- Create GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "KillHUD"
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Main Frame
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 220, 0, 150)
frame.Position = UDim2.new(0.5, -110, 0.5, -75)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0
frame.Parent = screenGui

-- Title
local title = Instance.new("TextLabel")
title.Size = UDim2.new(0, 220, 0, 30)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 20
title.Text = "Kill HUD"
title.Parent = frame

-- Button creator
local function createButton(name, posY, callback)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0, 200, 0, 40)
    button.Position = UDim2.new(0, 10, 0, posY)
    button.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.Text = name .. ": OFF"
    button.Parent = frame
    button.MouseButton1Click:Connect(callback)
    return button
end

-- Fly button
local flyButton = createButton("Fly", 40, function()
    flyEnabled = not flyEnabled
    flyButton.Text = "Fly: " .. (flyEnabled and "ON" or "OFF")
end)

-- Kill Aura button
local auraButton = createButton("Kill Aura", 90, function()
    auraEnabled = not auraEnabled
    auraButton.Text = "Kill Aura: " .. (auraEnabled and "ON" or "OFF")
end)

-- Disable All button
local disableAllButton = createButton("Disable All", 140, function()
    flyEnabled = false
    auraEnabled = false
    flyButton.Text = "Fly: OFF"
    auraButton.Text = "Kill Aura: OFF"
end)

-- Fly system
RunService.RenderStepped:Connect(function()
    if flyEnabled then
        local direction = Vector3.new(0,0,0)
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then
            direction = direction + hrp.CFrame.LookVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then
            direction = direction - hrp.CFrame.LookVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then
            direction = direction - hrp.CFrame.RightVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then
            direction = direction + hrp.CFrame.RightVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
            direction = direction + Vector3.new(0,1,0)
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
            direction = direction - Vector3.new(0,1,0)
        end
        if direction.Magnitude > 0 then
            hrp.Velocity = direction.Unit * flySpeed
        else
            hrp.Velocity = Vector3.new(0, hrp.Velocity.Y, 0)
        end
    end
end)

-- Kill Aura system
RunService.Heartbeat:Connect(function()
    if auraEnabled then
        local enemiesFolder = workspace:FindFirstChild("Enemies")
        if enemiesFolder then
            for _, enemy in pairs(enemiesFolder:GetChildren()) do
                if enemy:FindFirstChild("HumanoidRootPart") and enemy:FindFirstChild("Humanoid") then
                    local distance = (enemy.HumanoidRootPart.Position - hrp.Position).Magnitude
                    if distance <= attackRange then
                        enemy.Humanoid:TakeDamage(10)
                    end
                end
            end
        end
    end
end)
