# aimbot-first-time-writing-
hello this is GUY HUB
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "KLA is Noob Hub",
    LoadingTitle = "Loading...",
    LoadingSubtitle = "Powered by GUY HUB",
})

local Tab = Window:CreateTab("Main Tab")

local aimbotEnabled = false
local targetPart = "Head" -- Targeting the Head
local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera
local Players = game:GetService("Players")
local mouse = player:GetMouse()

-- Smooth Aiming Settings
local smoothness = 0.1 -- The higher the value, the smoother the aim transition

-- Function to Find Closest Opponent (Targeting)
local function getClosestEnemy()
    local closestPlayer = nil
    local shortestDistance = math.huge

    for _, otherPlayer in pairs(Players:GetPlayers()) do
        -- Skip if it's the local player, teammates, or neutral players
        if otherPlayer ~= player and otherPlayer.Character and otherPlayer.Character:FindFirstChild(targetPart) and not otherPlayer.Neutral then
            local targetPosition = otherPlayer.Character[targetPart].Position
            local screenPosition, onScreen = camera:WorldToViewportPoint(targetPosition)

            -- Check if the target is on screen
            if onScreen then
                local distance = (Vector2.new(mouse.X, mouse.Y) - Vector2.new(screenPosition.X, screenPosition.Y)).Magnitude
                if distance < shortestDistance then
                    shortestDistance = distance
                    closestPlayer = otherPlayer
                end
            end
        end
    end

    return closestPlayer
end

-- Function for Smooth Aiming (Transitioning toward the target gradually)
local function smoothAim(targetPlayer)
    if not targetPlayer then return end
    local targetPosition = targetPlayer.Character[targetPart].Position
    local cameraPosition = camera.CFrame.Position
    local direction = (targetPosition - cameraPosition).unit

    -- Smooth transition using Lerp (Linear interpolation)
    camera.CFrame = CFrame.new(cameraPosition, cameraPosition + direction * smoothness)
end

-- Auto-Shoot Mechanism
local function autoShoot()
    if not aimbotEnabled then return end
    local targetPlayer = getClosestEnemy()

    if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild(targetPart) then
        -- Simulate shooting (works if the player has a tool that can be activated)
        local tool = player.Character:FindFirstChildOfClass("Tool")
        if tool then
            if tool:FindFirstChild("Activate") then
                tool:Activate() -- Trigger the shooting action
            end
        end
    end
end

-- Auto Move Toward Target (Optional)
local function autoMove()
    if not aimbotEnabled then return end
    local targetPlayer = getClosestEnemy()

    if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild(targetPart) then
        local targetPosition = targetPlayer.Character[targetPart].Position
        local humanoid = player.Character:FindFirstChild("Humanoid")
        if humanoid then
            humanoid:MoveTo(targetPosition) -- Move toward the opponent
        end
    end
end

-- Start Aimbot Button
Tab:CreateButton({
    Name = "Start",
    Callback = function()
        aimbotEnabled = true
        Rayfield:Notify({Title = "Aimbot", Content = "Aimbot Enabled!", Duration = 2})

        -- Continuous aimbot loop (targeting, aiming, shooting, and moving)
        game:GetService("RunService").RenderStepped:Connect(function()
            local closestEnemy = getClosestEnemy()

            -- Smooth aim toward the closest enemy
            smoothAim(closestEnemy)
            
            -- Auto-shoot if the enemy is within the aiming range
            autoShoot()

            -- Optional: Automatically move toward the target
            autoMove()
        end)
    end,
})

-- Stop Aimbot Button
Tab:CreateButton({
    Name = "Stop",
    Callback = function()
        aimbotEnabled = false
        Rayfield:Notify({Title = "Aimbot", Content = "Aimbot Disabled!", Duration = 2})
    end,
})
