local AimbotSettings = {
    Enabled = false,
    AimFOV = 100,
    MaxDistance = 500,
    Smoothness = 0.3,
    Precision = 0.8,
    GuiVisible = true,
    TeamCheck = true,
}

local AllowedTools = {
    ["mp5"] = true,
    ["m4a1"] = true,
    ["fal"] = true,
    ["ak-47"] = true,
    ["rifle"] = true,
    ["remington"] = true,
    ["m9"] = true,
    ["bow"] = true,
    ["taser"] = true,
}

local function isUsingAllowedTool()
    local tool = player.Character and player.Character:FindFirstChildOfClass("Tool")
    if not tool then return false end
    local name = tool.Name:lower()
    for weapon, enabled in pairs(AllowedTools) do
        if enabled and name:find(weapon) then
            return true
        end
    end
    return false
end

local function isEnemy(other)
    if not AimbotSettings.TeamCheck then return true end
    if player.Team and other.Team then
        return player.Team ~= other.Team
    end
    return true
end

local function getClosestEnemy()
    if not isUsingAllowedTool() then return nil end
    local closest, shortest = nil, math.huge

    for _, enemy in pairs(Players:GetPlayers()) do
        if enemy ~= player
        and isEnemy(enemy)
        and enemy.Character
        and enemy.Character:FindFirstChild("HumanoidRootPart")
        and player.Character
        and player.Character:FindFirstChild("HumanoidRootPart") then

            local hrp = enemy.Character.HumanoidRootPart
            local screenPos, visible = camera:WorldToScreenPoint(hrp.Position)

            if visible then
                local dist2D = (Vector2.new(mouse.X, mouse.Y) - Vector2.new(screenPos.X, screenPos.Y)).Magnitude
                local dist3D = (player.Character.HumanoidRootPart.Position - hrp.Position).Magnitude

                if dist2D < AimbotSettings.AimFOV
                and dist3D < AimbotSettings.MaxDistance
                and dist2D < shortest then
                    shortest = dist2D
                    closest = hrp
                end
            end
        end
    end
    return closest
end

local function aimAtTarget()
    local target = getClosestEnemy()
    if target then
        local cf = camera.CFrame
        camera.CFrame = cf:Lerp(CFrame.new(cf.Position, target.Position), AimbotSettings.Smoothness)
    end
end

local screenGui = Instance.new("ScreenGui", playerGui)
screenGui.ResetOnSpawn = false

local mainFrame = Instance.new("Frame", screenGui)
mainFrame.Size = UDim2.new(0, 380, 0, 700)
mainFrame.Position = UDim2.new(0.5, -190, 0.5, -350)
mainFrame.BackgroundColor3 = Color3.fromRGB(10,10,10)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0,14)

local stroke = Instance.new("UIStroke", mainFrame)
stroke.Thickness = 3
local hue = 0
RunService.RenderStepped:Connect(function(dt)
    hue = (hue + dt * 0.2) % 1
    stroke.Color = Color3.fromHSV(hue,1,1)
end)

local title = Instance.new("TextLabel", mainFrame)
title.Size = UDim2.new(1,0,0,45)
title.BackgroundTransparency = 1
title.Text = "👑 Aimbot By Jairox 👑"
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.TextColor3 = Color3.new(1,1,1)

local statusLabel = Instance.new("TextLabel", mainFrame)
statusLabel.Size = UDim2.new(1,0,0,25)
statusLabel.Position = UDim2.new(0,0,0,50)
statusLabel.BackgroundTransparency = 1
statusLabel.Text = "Estado: Desactivado"
statusLabel.Font = Enum.Font.Gotham
statusLabel.TextScaled = true
statusLabel.TextColor3 = Color3.fromRGB(200,200,200)

local function createSetting(posY, min, max, default, step, text, callback)
    local label = Instance.new("TextLabel", mainFrame)
    label.Size = UDim2.new(1,-20,0,20)
    label.Position = UDim2.new(0,10,0,posY-20)
    label.BackgroundTransparency = 1
    label.Text = text
    label.Font = Enum.Font.Gotham
    label.TextScaled = true
    label.TextColor3 = Color3.fromRGB(180,180,180)
    label.TextXAlignment = Enum.TextXAlignment.Left

    local frame = Instance.new("Frame", mainFrame)
    frame.Size = UDim2.new(1,-20,0,40)
    frame.Position = UDim2.new(0,10,0,posY)
    frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0,8)

    local valueLabel = Instance.new("TextLabel", frame)
    valueLabel.Size = UDim2.new(0,70,0,25)
    valueLabel.Position = UDim2.new(0.5,-35,0,8)
    valueLabel.BackgroundTransparency = 1
    valueLabel.Font = Enum.Font.GothamBold
    valueLabel.TextScaled = true
    valueLabel.TextColor3 = Color3.new(1,1,1)

    local current = default
    local function update(val)
        current = math.clamp(val, min, max)
        valueLabel.Text = tostring(math.floor(current*100)/100)
        callback(current)
    end

    for _, data in pairs({
        { "-", -step, Color3.fromRGB(200,70,70), 5 },
        { "+", step, Color3.fromRGB(70,200,70), 1 }
    }) do
        local btn = Instance.new("TextButton", frame)
        btn.Size = UDim2.new(0,30,0,25)
        btn.Position = UDim2.new(data[4]==5 and 0 or 1, data[4]==5 and 5 or -105, 0, 8)
        btn.Text = data[1]
        btn.Font = Enum.Font.GothamBold
        btn.TextScaled = true
        btn.BackgroundColor3 = data[3]
        btn.TextColor3 = Color3.new(1,1,1)
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0,6)
        btn.MouseButton1Click:Connect(function()
            update(current + data[2])
        end)
    end

    update(default)
end

createSetting(90, 10, 300, AimbotSettings.AimFOV, 5, "FOV", function(v) AimbotSettings.AimFOV = v end)
createSetting(150, 50, 1000, AimbotSettings.MaxDistance, 50, "Distancia Máxima", function(v) AimbotSettings.MaxDistance = v end)
createSetting(210, 0.01, 1, AimbotSettings.Smoothness, 0.05, "Suavidad", function(v) AimbotSettings.Smoothness = v end)
createSetting(270, 0, 1, AimbotSettings.Precision, 0.05, "Precisión", function(v) AimbotSettings.Precision = v end)

local toolsFrame = Instance.new("Frame", mainFrame)
toolsFrame.Size = UDim2.new(1,-20,0,190)
toolsFrame.Position = UDim2.new(0,10,0,320)
toolsFrame.BackgroundColor3 = Color3.fromRGB(15,15,15)
Instance.new("UICorner", toolsFrame).CornerRadius = UDim.new(0,10)

local toolsTitle = Instance.new("TextLabel", toolsFrame)
toolsTitle.Size = UDim2.new(1,0,0,25)
toolsTitle.BackgroundTransparency = 1
toolsTitle.Text = "🔫 Tools Permitidas"
toolsTitle.Font = Enum.Font.GothamBold
toolsTitle.TextScaled = true
toolsTitle.TextColor3 = Color3.fromRGB(255,255,255)

local function toolButton(text, key, x, y)
    local b = Instance.new("TextButton", toolsFrame)
    b.Size = UDim2.new(0.45,0,0,30)
    b.Position = UDim2.new(x,0,0,y)
    b.Text = text.." ✔"
    b.Font = Enum.Font.GothamBold
    b.TextScaled = true
    b.TextColor3 = Color3.new(1,1,1)
    b.BackgroundColor3 = Color3.fromRGB(60,160,60)
    Instance.new("UICorner", b).CornerRadius = UDim.new(0,6)

    b.MouseButton1Click:Connect(function()
        AllowedTools[key] = not AllowedTools[key]
        b.Text = text .. (AllowedTools[key] and " ✔" or " ✖")
        b.BackgroundColor3 = AllowedTools[key] and Color3.fromRGB(60,160,60) or Color3.fromRGB(160,60,60)
    end)
end

toolButton("MP5","mp5",0.03,35)
toolButton("M4A1","m4a1",0.52,35)
toolButton("FAL","fal",0.03,70)
toolButton("AK-47","ak-47",0.52,70)
toolButton("Sniper","rifle",0.03,105)
toolButton("Remington","remington",0.52,105)
toolButton("M9","m9",0.03,140)
toolButton("Bow","bow",0.52,140)
toolButton("Taser","taser",0.275,170)

local teamBtn = Instance.new("TextButton", mainFrame)
teamBtn.Size = UDim2.new(1,-20,0,30)
teamBtn.Position = UDim2.new(0,10,0,540)
teamBtn.Text = "Team Check: ON"
teamBtn.Font = Enum.Font.GothamBold
teamBtn.TextScaled = true
teamBtn.BackgroundColor3 = Color3.fromRGB(60,160,60)
teamBtn.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", teamBtn).CornerRadius = UDim.new(0,8)

teamBtn.MouseButton1Click:Connect(function()
    AimbotSettings.TeamCheck = not AimbotSettings.TeamCheck
    teamBtn.Text = "Team Check: " .. (AimbotSettings.TeamCheck and "ON" or "OFF")
    teamBtn.BackgroundColor3 = AimbotSettings.TeamCheck and Color3.fromRGB(60,160,60) or Color3.fromRGB(160,60,60)
end)

UserInputService.InputBegan:Connect(function(input,gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.E then
        AimbotSettings.Enabled = not AimbotSettings.Enabled
        statusLabel.Text = "Estado: " .. (AimbotSettings.Enabled and "Activado" or "Desactivado")
    elseif input.KeyCode == Enum.KeyCode.Y then
        screenGui.Enabled = not screenGui.Enabled
    end
end)

RunService.RenderStepped:Connect(function()
    if AimbotSettings.Enabled and isUsingAllowedTool() then
        aimAtTarget()
    end
end)
