-- ============================================================
-- REDX HUB V7 MOBILE - FIXED (ESP fast / Fly fixed / VFX)
-- ============================================================

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local TeleportService = game:GetService("TeleportService")

local player = Players.LocalPlayer
local mouse = player:GetMouse()

local RED   = Color3.fromRGB(235,25,40)
local DARK  = Color3.fromRGB(18,18,23)
local PANEL = Color3.fromRGB(40,40,48)
local WHITE = Color3.new(1,1,1)
local GRAY  = Color3.fromRGB(150,150,155)
local GREEN = Color3.fromRGB(60,200,100)

local character, humanoid, root

-- СТАНИ ФУНКЦІЙ
local flying, noclip, infiniteJump = false, false, false
local espEnabled, bhopEnabled, spinEnabled, thirdPersonEnabled = false, false, false, false
local aimbotEnabled, teamCheck, visibleCheck = false, false, true
local hitboxEnabled, antiAFKEnabled, autoRespawn, nightMode = false, false, false, false
local flySpeed, spinSpeed = 60, 10
local flyVerticalSpeed = 1
local fov, smoothing = 150, 1
local aimPart = "Head"
local hitboxSize, hitboxTrans = 5, 0.7
local espFillColor, espFillTrans = RED, 0.55
local normalGravity = workspace.Gravity

-- стани для перепідключення після смерті
local currentWalkSpeed, currentJumpPower = 16, 50
local speedHackEnabled, gravityEnabled, gravityValue = false, false, normalGravity

local flyConnection, spinAttachment, spinVelocity, spinGyro, spinConnection, cameraLockConnection
local hitboxConnection, aimbotConnection, antiafkConnection
local originalCollision = {}
local originalHitbox = {}

local FOVring = nil
local hasDrawing = typeof(Drawing) == "table" and Drawing.new ~= nil

-- ============================================================
-- ДОПОМІЖНЕ
-- ============================================================
local function getCharacter()
    character = player.Character or player.CharacterAdded:Wait()
    humanoid = character:WaitForChild("Humanoid")
    root = character:WaitForChild("HumanoidRootPart")
end
getCharacter()

local function tween(obj, props, time)
    TweenService:Create(obj, TweenInfo.new(time or .18, Enum.EasingStyle.Quart), props):Play()
end

-- ============================================================
-- GUI
-- ============================================================
local gui = Instance.new("ScreenGui")
gui.Name = "REDX_HUB"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = player:WaitForChild("PlayerGui")

-- WATERMARK
local watermark = Instance.new("TextButton")
watermark.Size = UDim2.fromOffset(110,36)
watermark.Position = UDim2.fromOffset(12,90)
watermark.BackgroundColor3 = RED
watermark.Text = "REDX HUB"
watermark.TextColor3 = WHITE
watermark.TextSize = 14
watermark.Font = Enum.Font.GothamBold
watermark.AutoButtonColor = false
watermark.Parent = gui
Instance.new("UICorner",watermark).CornerRadius = UDim.new(0,7)

-- MAIN
local main = Instance.new("Frame")
main.Size = UDim2.fromOffset(280,320)
main.Position = UDim2.new(.5,-140,.5,-160)
main.BackgroundColor3 = DARK
main.Visible = false
main.ClipsDescendants = true
main.Parent = gui
Instance.new("UICorner",main).CornerRadius = UDim.new(0,10)
local outline = Instance.new("UIStroke",main)
outline.Color = RED
outline.Thickness = 2

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,-44,0,32)
title.Position = UDim2.fromOffset(10,0)
title.BackgroundTransparency = 1
title.Text = "REDX HUB V7"
title.TextColor3 = RED
title.TextSize = 17
title.Font = Enum.Font.GothamBold
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = main

local close = Instance.new("TextButton")
close.Size = UDim2.fromOffset(28,28)
close.Position = UDim2.new(1,-33,0,2)
close.BackgroundColor3 = RED
close.Text = "X"
close.TextColor3 = WHITE
close.Font = Enum.Font.GothamBold
close.TextSize = 15
close.Parent = main
Instance.new("UICorner",close).CornerRadius = UDim.new(0,7)

local tabBar = Instance.new("Frame")
tabBar.Size = UDim2.fromOffset(52,1)
tabBar.Position = UDim2.fromOffset(6,36)
tabBar.BackgroundTransparency = 1
tabBar.Parent = main
local tabLayout = Instance.new("UIListLayout")
tabLayout.Padding = UDim.new(0,5)
tabLayout.Parent = tabBar

local content = Instance.new("Frame")
content.Size = UDim2.new(1,-64,1,-44)
content.Position = UDim2.fromOffset(62,36)
content.BackgroundTransparency = 1
content.Parent = main

local pages, tabButtons = {}, {}

local function makeTab(name)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1,0,0,30)
    btn.BackgroundColor3 = PANEL
    btn.Text = name
    btn.TextColor3 = GRAY
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 10
    btn.Parent = tabBar
    Instance.new("UICorner",btn).CornerRadius = UDim.new(0,6)

    local page = Instance.new("ScrollingFrame")
    page.Size = UDim2.fromScale(1,1)
    page.BackgroundTransparency = 1
    page.BorderSizePixel = 0
    page.ScrollBarThickness = 2
    page.ScrollBarImageColor3 = RED
    page.AutomaticCanvasSize = Enum.AutomaticSize.Y
    page.CanvasSize = UDim2.new()
    page.Visible = false
    page.Parent = content
    local pl = Instance.new("UIListLayout")
    pl.Padding = UDim.new(0,4)
    pl.Parent = page

    tabButtons[name] = btn
    pages[name] = page

    btn.MouseButton1Click:Connect(function()
        for n,p in pairs(pages) do p.Visible = n == name end
        for _,b in pairs(tabButtons) do
            tween(b,{BackgroundColor3 = PANEL},.12)
            b.TextColor3 = GRAY
        end
        tween(btn,{BackgroundColor3 = RED},.12)
        btn.TextColor3 = WHITE
    end)
    return page
end

local combatPage   = makeTab("БІЙ")
local movementPage = makeTab("РУХ")
local visualPage   = makeTab("ВІЗУАЛ")
local miscPage     = makeTab("ІНШЕ")

pages["БІЙ"].Visible = true
tabButtons["БІЙ"].BackgroundColor3 = RED
tabButtons["БІЙ"].TextColor3 = WHITE

local function openMenu()
    main.Visible = true
    main.Size = UDim2.fromOffset(260,290)
    tween(main,{Size=UDim2.fromOffset(280,320)},.18)
end
local function closeMenu()
    tween(main,{Size=UDim2.fromOffset(260,290)},.15)
    task.delay(.15,function() main.Visible = false end)
end
close.MouseButton1Click:Connect(closeMenu)

-- DRAG WATERMARK
local dragging, moved = false, false
local dragStart, startPos, activeInput
watermark.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch
    or input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging, moved = true, false
        activeInput = input
        dragStart = input.Position
        startPos = watermark.Position
    end
end)
UIS.InputChanged:Connect(function(input)
    if not dragging then return end
    if input == activeInput or input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        if delta.Magnitude > 8 then moved = true end
        if moved then
            watermark.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset+delta.X,
                startPos.Y.Scale, startPos.Y.Offset+delta.Y)
        end
    end
end)
UIS.InputEnded:Connect(function(input)
    if not dragging then return end
    if input == activeInput
    or input.UserInputType == Enum.UserInputType.MouseButton1
    or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
        if not moved then
            if main.Visible then closeMenu() else openMenu() end
        end
        activeInput = nil
    end
end)

-- DRAG MENU
local menuDragging = false
local menuStart, menuPosition
title.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch
    or input.UserInputType == Enum.UserInputType.MouseButton1 then
        menuDragging = true
        menuStart = input.Position
        menuPosition = main.Position
    end
end)
UIS.InputChanged:Connect(function(input)
    if menuDragging and (input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - menuStart
        main.Position = UDim2.new(menuPosition.X.Scale, menuPosition.X.Offset+delta.X,
            menuPosition.Y.Scale, menuPosition.Y.Offset+delta.Y)
    end
end)
UIS.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
    or input.UserInputType == Enum.UserInputType.Touch then
        menuDragging = false
    end
end)

-- ============================================================
-- КОНСТРУКТОРИ ЕЛЕМЕНТІВ
-- ============================================================
local function makeFeature(parent, name, onToggle, buildSettings)
    local holder = Instance.new("Frame")
    holder.Size = UDim2.new(1,0,0,0)
    holder.AutomaticSize = Enum.AutomaticSize.Y
    holder.BackgroundTransparency = 1
    holder.Parent = parent
    local hl = Instance.new("UIListLayout")
    hl.Padding = UDim.new(0,3)
    hl.Parent = holder

    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1,0,0,34)
    b.BackgroundColor3 = PANEL
    b.Text = name.." : OFF"
    b.TextColor3 = WHITE
    b.Font = Enum.Font.GothamBold
    b.TextSize = 11
    b.Parent = holder
    Instance.new("UICorner",b).CornerRadius = UDim.new(0,6)

    local settings = Instance.new("Frame")
    settings.Size = UDim2.new(1,0,0,0)
    settings.AutomaticSize = Enum.AutomaticSize.Y
    settings.BackgroundColor3 = Color3.fromRGB(28,28,34)
    settings.Visible = false
    settings.ClipsDescendants = true
    settings.Parent = holder
    Instance.new("UICorner",settings).CornerRadius = UDim.new(0,6)
    local sl = Instance.new("UIListLayout")
    sl.Padding = UDim.new(0,3)
    sl.Parent = settings
    local pad = Instance.new("UIPadding")
    pad.PaddingTop = UDim.new(0,4)
    pad.PaddingBottom = UDim.new(0,4)
    pad.PaddingLeft = UDim.new(0,4)
    pad.PaddingRight = UDim.new(0,4)
    pad.Parent = settings

    if buildSettings then buildSettings(settings) end

    local enabled = false
    b.MouseButton1Click:Connect(function()
        enabled = not enabled
        b.Text = name..(enabled and " : ON" or " : OFF")
        tween(b,{BackgroundColor3 = enabled and RED or PANEL})
        settings.Visible = enabled
        if onToggle then onToggle(enabled) end
    end)
    return b
end

local function makeSimpleToggle(parent, name, callback)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1,0,0,34)
    b.BackgroundColor3 = PANEL
    b.Text = name.." : OFF"
    b.TextColor3 = WHITE
    b.Font = Enum.Font.GothamBold
    b.TextSize = 11
    b.Parent = parent
    Instance.new("UICorner",b).CornerRadius = UDim.new(0,6)
    local enabled = false
    b.MouseButton1Click:Connect(function()
        enabled = not enabled
        b.Text = name..(enabled and " : ON" or " : OFF")
        tween(b,{BackgroundColor3 = enabled and RED or PANEL})
        callback(enabled)
    end)
    return b
end

local function makeAction(parent, name, callback)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1,0,0,34)
    b.BackgroundColor3 = Color3.fromRGB(60,45,50)
    b.Text = name
    b.TextColor3 = WHITE
    b.Font = Enum.Font.GothamBold
    b.TextSize = 11
    b.Parent = parent
    Instance.new("UICorner",b).CornerRadius = UDim.new(0,6)
    b.MouseButton1Click:Connect(callback)
end

local function makeSlider(parent, name, min, max, default, callback)
    local holder = Instance.new("Frame")
    holder.Size = UDim2.new(1,0,0,48)
    holder.BackgroundColor3 = Color3.fromRGB(50,50,58)
    holder.Parent = parent
    Instance.new("UICorner",holder).CornerRadius = UDim.new(0,6)

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1,-16,0,22)
    label.Position = UDim2.fromOffset(8,2)
    label.BackgroundTransparency = 1
    label.Text = name..": "..default
    label.TextColor3 = WHITE
    label.Font = Enum.Font.GothamBold
    label.TextSize = 10
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = holder

    local bar = Instance.new("Frame")
    bar.Size = UDim2.new(1,-16,0,8)
    bar.Position = UDim2.fromOffset(8,32)
    bar.BackgroundColor3 = Color3.fromRGB(65,65,72)
    bar.Parent = holder
    Instance.new("UICorner",bar).CornerRadius = UDim.new(1,0)

    local fill = Instance.new("Frame")
    fill.Size = UDim2.new((default-min)/(max-min),0,1,0)
    fill.BackgroundColor3 = RED
    fill.Parent = bar
    Instance.new("UICorner",fill).CornerRadius = UDim.new(1,0)

    local knob = Instance.new("Frame")
    knob.Size = UDim2.fromOffset(13,13)
    knob.AnchorPoint = Vector2.new(.5,.5)
    knob.Position = UDim2.new((default-min)/(max-min),0,.5,0)
    knob.BackgroundColor3 = WHITE
    knob.Parent = bar
    Instance.new("UICorner",knob).CornerRadius = UDim.new(1,0)

    local sliding, touchInput = false, nil

    local function update(x)
        local percent = math.clamp((x-bar.AbsolutePosition.X)/bar.AbsoluteSize.X,0,1)
        local value = math.floor(min+(max-min)*percent)
        fill.Size = UDim2.new(percent,0,1,0)
        knob.Position = UDim2.new(percent,0,.5,0)
        label.Text = name..": "..value
        callback(value)
    end

    bar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Touch
        or input.UserInputType == Enum.UserInputType.MouseButton1 then
            sliding = true
            touchInput = input
            update(input.Position.X)
        end
    end)
    UIS.InputChanged:Connect(function(input)
        if sliding and (input == touchInput or input.UserInputType == Enum.UserInputType.MouseMovement) then
            update(input.Position.X)
        end
    end)
    UIS.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input == touchInput then
            sliding, touchInput = false, nil
        end
    end)
end

local function makeSelector(parent, name, options, default, callback)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1,0,0,30)
    b.BackgroundColor3 = Color3.fromRGB(50,50,58)
    b.TextColor3 = WHITE
    b.Font = Enum.Font.GothamBold
    b.TextSize = 10
    b.Parent = parent
    Instance.new("UICorner",b).CornerRadius = UDim.new(0,6)

    local index = table.find(options, default) or 1
    b.Text = name..": "..options[index]
    callback(options[index])

    b.MouseButton1Click:Connect(function()
        index = index % #options + 1
        b.Text = name..": "..options[index]
        tween(b,{BackgroundColor3 = RED},.1)
        task.delay(.12,function() tween(b,{BackgroundColor3 = Color3.fromRGB(50,50,58)},.1) end)
        callback(options[index])
    end)
end

-- ============================================================
-- AIMBOT
-- ============================================================
local function getClosest(cframe)
    local ray = Ray.new(cframe.Position, cframe.LookVector).Unit
    local target, mag = nil, math.huge
    for _,v in ipairs(Players:GetPlayers()) do
        if v.Character
        and v.Character:FindFirstChild(aimPart)
        and v.Character:FindFirstChild("Humanoid")
        and v ~= player
        and (v.Team ~= player.Team or (not teamCheck))
        and v.Character.Humanoid.Health > 0 then
            local partPos = v.Character[aimPart].Position
            local magBuf = (partPos - ray:ClosestPoint(partPos)).Magnitude
            if visibleCheck and magBuf < mag then
                local camPos = cframe.Position
                local toPart = partPos - camPos
                local dist = toPart.Magnitude
                local rayParams = RaycastParams.new()
                rayParams.FilterDescendantsInstances = {player.Character, v.Character}
                rayParams.FilterType = Enum.RaycastFilterType.Exclude
                rayParams.IgnoreWater = true
                local hit = workspace:Raycast(camPos, toPart.Unit * (dist - 1), rayParams)
                if hit and hit.Instance and hit.Instance.CanCollide then
                    magBuf = math.huge
                end
            end
            if magBuf < mag then
                mag = magBuf
                target = v
            end
        end
    end
    return target
end

if hasDrawing then
    FOVring = Drawing.new("Circle")
    FOVring.Visible = false
    FOVring.Thickness = 1.5
    FOVring.Transparency = 1
    FOVring.Color = Color3.fromRGB(255,128,128)
end

aimbotConnection = RunService.RenderStepped:Connect(function()
    if hasDrawing and FOVring then
        FOVring.Position = workspace.CurrentCamera.ViewportSize/2
        FOVring.Radius = fov
        FOVring.Visible = aimbotEnabled
    end
    if not aimbotEnabled then return end
    local cam = workspace.CurrentCamera
    local zz = cam.ViewportSize/2
    local curTar = getClosest(cam.CFrame)
    if curTar and curTar.Character and curTar.Character:FindFirstChild(aimPart) then
        local sp = cam:WorldToScreenPoint(curTar.Character[aimPart].Position)
        if sp.Z > 0 and (Vector2.new(sp.X,sp.Y) - zz).Magnitude < fov then
            cam.CFrame = cam.CFrame:Lerp(
                CFrame.new(cam.CFrame.Position, curTar.Character[aimPart].Position),
                math.clamp(smoothing / 100, 0, 1))
        end
    end
end)

-- ============================================================
-- HITBOX EXPANDER
-- ============================================================
local function setHitbox(state)
    hitboxEnabled = state
    if hitboxConnection then hitboxConnection:Disconnect() hitboxConnection = nil end
    if state then
        hitboxConnection = RunService.Heartbeat:Connect(function()
            for _,v in ipairs(Players:GetPlayers()) do
                if v ~= player and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
                    local hrp = v.Character.HumanoidRootPart
                    if not originalHitbox[v] then
                        originalHitbox[v] = {hrp.Size, hrp.Transparency, hrp.CanCollide, hrp.Material}
                    end
                    hrp.Size = Vector3.new(hitboxSize, hitboxSize, hitboxSize)
                    hrp.Transparency = hitboxTrans
                    hrp.CanCollide = false
                    hrp.Material = Enum.Material.Neon
                    hrp.Color = RED
                end
            end
        end)
    else
        for v, data in pairs(originalHitbox) do
            if v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
                local hrp = v.Character.HumanoidRootPart
                hrp.Size = data[1]
                hrp.Transparency = data[2]
                hrp.CanCollide = data[3]
                hrp.Material = data[4]
            end
        end
        table.clear(originalHitbox)
    end
end

-- ============================================================
-- FLY (FIXED - без дублікатів петель)
-- ============================================================
local tpwalking = false
local flyLoopToken = 0

local FLY_STATES = {
    Enum.HumanoidStateType.Climbing, Enum.HumanoidStateType.FallingDown,
    Enum.HumanoidStateType.Flying, Enum.HumanoidStateType.Freefall,
    Enum.HumanoidStateType.GettingUp, Enum.HumanoidStateType.Jumping,
    Enum.HumanoidStateType.Landed, Enum.HumanoidStateType.Physics,
    Enum.HumanoidStateType.PlatformStanding, Enum.HumanoidStateType.Ragdoll,
    Enum.HumanoidStateType.Running, Enum.HumanoidStateType.RunningNoPhysics,
    Enum.HumanoidStateType.Seated, Enum.HumanoidStateType.StrafingNoPhysics,
    Enum.HumanoidStateType.Swimming,
}

local function setFlyStates(enabled)
    if not humanoid or not humanoid.Parent then return end
    for _, s in ipairs(FLY_STATES) do
        humanoid:SetStateEnabled(s, enabled)
    end
end

local flyGyro, flyVel

local function startTpWalk()
    tpwalking = false
    flyLoopToken = flyLoopToken + 1
    local myToken = flyLoopToken
    task.wait()

    if not flying then return end

    tpwalking = true
    task.spawn(function()
        local hb = RunService.Heartbeat
        while tpwalking and flyLoopToken == myToken do
            hb:Wait()
            if not flying or flyLoopToken ~= myToken then break end

            local chr = player.Character
            local hum = chr and chr:FindFirstChildWhichIsA("Humanoid")
            local rt = chr and chr:FindFirstChild("HumanoidRootPart")
            if not chr or not hum or not hum.Parent or not rt then break end

            local cam = workspace.CurrentCamera
            if cam and hum.MoveDirection.Magnitude > 0 then
                local look = cam.CFrame.LookVector
                local right = cam.CFrame.RightVector
                local flatLook = Vector3.new(look.X, 0, look.Z)
                local flatRight = Vector3.new(right.X, 0, right.Z)
                if flatLook.Magnitude > 0 then flatLook = flatLook.Unit end
                if flatRight.Magnitude > 0 then flatRight = flatRight.Unit end
                local fwd = math.clamp(hum.MoveDirection:Dot(flatLook), -1, 1)
                local side = math.clamp(hum.MoveDirection:Dot(flatRight), -1, 1)
                local dir = look * fwd + flatRight * side
                if dir.Magnitude > 0 then
                    chr:TranslateBy(dir.Unit * (flySpeed / 60) * 0.9)
                end
            end
            if hum.Jump then
                rt.CFrame = rt.CFrame + Vector3.new(0, 0.6 * flyVerticalSpeed, 0)
            end
        end
    end)
end

local function stopFly()
    if not flying then return end
    flying, tpwalking = false, false
    flyLoopToken = flyLoopToken + 1
    if flyConnection then flyConnection:Disconnect() flyConnection = nil end
    if flyGyro then flyGyro:Destroy() flyGyro = nil end
    if flyVel then flyVel:Destroy() flyVel = nil end
    setFlyStates(true)
    if humanoid and humanoid.Parent then
        humanoid.PlatformStand = false
        humanoid:ChangeState(Enum.HumanoidStateType.RunningNoPhysics)
    end
    local char = player.Character
    if char then
        local animate = char:FindFirstChild("Animate")
        if animate then animate.Disabled = false end
    end
end

local function startFly()
    if flying then return end
    getCharacter()
    flying = true
    flyVel = Instance.new("BodyVelocity")
    flyVel.MaxForce = Vector3.new(9e9, 9e9, 9e9)
    flyVel.Velocity = Vector3.new(0, 0.1, 0)
    flyVel.Parent = root
    flyGyro = Instance.new("BodyGyro")
    flyGyro.P = 9e4
    flyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
    flyGyro.CFrame = root.CFrame
    flyGyro.Parent = root
    startTpWalk()
    local animate = character:FindFirstChild("Animate")
    if animate then animate.Disabled = true end
    for _, v in ipairs(humanoid:GetPlayingAnimationTracks()) do v:AdjustSpeed(0) end
    setFlyStates(false)
    humanoid.PlatformStand = true
    flyConnection = RunService.Heartbeat:Connect(function()
        if not flying or not root or not root.Parent then return end
        local cam = workspace.CurrentCamera
        if cam and flyGyro then
            local look = cam.CFrame.LookVector
            flyGyro.CFrame = CFrame.new(root.Position, root.Position + Vector3.new(look.X, 0, look.Z))
        end
        if UIS:IsKeyDown(Enum.KeyCode.LeftShift) and character then
            character:TranslateBy(Vector3.new(0, -0.6 * flyVerticalSpeed, 0))
        end
    end)
end

-- ============================================================
-- NOCLIP
-- ============================================================
local function setNoclip(state)
    noclip = state
    if not state then
        for part, old in pairs(originalCollision) do
            if part.Parent then part.CanCollide = old end
        end
        table.clear(originalCollision)
    end
end
RunService.Stepped:Connect(function()
    if noclip and character then
        for _, part in ipairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                if originalCollision[part] == nil then originalCollision[part] = part.CanCollide end
                part.CanCollide = false
            end
        end
    end
end)

-- ============================================================
-- BHOP
-- ============================================================
RunService.Heartbeat:Connect(function()
    if not bhopEnabled or not humanoid or not humanoid.Parent then return end
    if humanoid.FloorMaterial ~= Enum.Material.Air then
        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

-- ============================================================
-- SPIN (FIXED)
-- ============================================================
local function setSpin(state)
    spinEnabled = state
    if spinConnection then spinConnection:Disconnect() spinConnection = nil end
    if state then
        getCharacter()
        spinAttachment = Instance.new("Attachment")
        spinAttachment.Parent = root
        spinVelocity = Instance.new("AngularVelocity")
        spinVelocity.Attachment0 = spinAttachment
        spinVelocity.RelativeTo = Enum.ActuatorRelativeTo.World
        spinVelocity.MaxTorque = math.huge
        spinVelocity.AngularVelocity = Vector3.new(0, spinSpeed, 0)
        spinVelocity.Parent = root
        spinGyro = Instance.new("BodyGyro")
        spinGyro.P = 9e4
        spinGyro.MaxTorque = Vector3.new(9e9, 0, 9e9)
        spinGyro.CFrame = root.CFrame
        spinGyro.Parent = root
        humanoid.AutoRotate = false
        humanoid:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
        humanoid:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
        spinConnection = RunService.Heartbeat:Connect(function()
            if spinGyro and root and root.Parent then
                local cf = root.CFrame
                spinGyro.CFrame = CFrame.new(cf.Position)
            end
        end)
    else
        if spinVelocity then spinVelocity:Destroy() spinVelocity = nil end
        if spinAttachment then spinAttachment:Destroy() spinAttachment = nil end
        if spinGyro then spinGyro:Destroy() spinGyro = nil end
        if humanoid then
            humanoid.AutoRotate = true
            humanoid:SetStateEnabled(Enum.HumanoidStateType.FallingDown, true)
            humanoid:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, true)
        end
    end
end

-- ============================================================
-- 3RD PERSON
-- ============================================================
local function setThirdPerson(state)
    thirdPersonEnabled = state
    if state then
        player.CameraMode = Enum.CameraMode.Classic
        player.CameraMinZoomDistance = 5
        player.CameraMaxZoomDistance = 15
        cameraLockConnection = RunService.RenderStepped:Connect(function()
            if player.CameraMinZoomDistance < 5 then
                player.CameraMinZoomDistance = 5
                player.CameraMaxZoomDistance = 15
                player.CameraMode = Enum.CameraMode.Classic
            end
        end)
    else
        if cameraLockConnection then cameraLockConnection:Disconnect() cameraLockConnection = nil end
        player.CameraMinZoomDistance = 0.5
        player.CameraMaxZoomDistance = 400
    end
end

-- ============================================================
-- ESP (FAST - миттєве оновлення)
-- ============================================================
local espCache = {}

local function createESP(plr)
    if not plr.Character or espCache[plr] then return end
    local h = Instance.new("Highlight")
    h.Name = "REDX_ESP"
    h.FillColor = espFillColor
    h.OutlineColor = WHITE
    h.FillTransparency = espFillTrans
    h.OutlineTransparency = 0
    h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    h.Parent = plr.Character
    espCache[plr] = h
end

local function removeESP(plr)
    if espCache[plr] then
        espCache[plr]:Destroy()
        espCache[plr] = nil
    end
    if plr.Character then
        local old = plr.Character:FindFirstChild("REDX_ESP")
        if old then old:Destroy() end
    end
end

local function refreshESP()
    if not espEnabled then
        for plr in pairs(espCache) do removeESP(plr) end
        for _, plr in ipairs(Players:GetPlayers()) do removeESP(plr) end
        return
    end
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= player and plr.Character and plr.Character:FindFirstChild("Humanoid") then
            if not espCache[plr] or not espCache[plr].Parent then
                createESP(plr)
            else
                espCache[plr].FillColor = espFillColor
                espCache[plr].FillTransparency = espFillTrans
            end
        else
            removeESP(plr)
        end
    end
end

local function hookPlayer(plr)
    if plr == player then return end
    plr.CharacterAdded:Connect(function(char)
        if espEnabled then
            char:WaitForChild("Humanoid", 5)
            task.wait(0.05)
            if espEnabled and plr.Character == char then
                createESP(plr)
            end
        end
    end)
    plr.CharacterRemoving:Connect(function()
        removeESP(plr)
    end)
    if espEnabled and plr.Character then createESP(plr) end
end

Players.PlayerAdded:Connect(hookPlayer)
Players.PlayerRemoving:Connect(function(plr) removeESP(plr) end)
for _, plr in ipairs(Players:GetPlayers()) do hookPlayer(plr) end

RunService.Heartbeat:Connect(function()
    if espEnabled then refreshESP() end
end)

local function updateESP()
    refreshESP()
end

-- ============================================================
-- ANTI AFK
-- ============================================================
local function setAntiAFK(state)
    antiAFKEnabled = state
    if antiafkConnection then antiafkConnection:Disconnect() antiafkConnection = nil end
    if state then
        antiafkConnection = RunService.Heartbeat:Connect(function()
            for _, c in ipairs(getconnections(player.Idled)) do
                if c.Enabled then c:Disable() end
            end
        end)
    end
end

-- ============================================================
-- NIGHT MODE
-- ============================================================
local function setNightMode(state)
    nightMode = state
    if state then
        tween(Lighting, {ClockTime = 0, Ambient = Color3.fromRGB(30,30,40)}, .5)
        Lighting.FogEnd = 200
        Lighting.FogColor = Color3.fromRGB(10,10,15)
    else
        tween(Lighting, {ClockTime = 14, Ambient = Color3.fromRGB(70,70,80)}, .5)
        Lighting.FogEnd = 100000
    end
end

-- ============================================================
-- VISUAL EFFECTS
-- ============================================================
local trailEnabled = false
local auraEnabled = false
local trail, trailA0, trailA1
local aura, auraLight

local function setupTrail(state)
    trailEnabled = state
    if state and root then
        if trail then trail:Destroy() end
        if trailA0 then trailA0:Destroy() end
        if trailA1 then trailA1:Destroy() end
        trailA0 = Instance.new("Attachment")
        trailA0.Name = "REDX_TrailA0"
        trailA0.Position = Vector3.new(0, 0.5, 0)
        trailA0.Parent = root
        trailA1 = Instance.new("Attachment")
        trailA1.Name = "REDX_TrailA1"
        trailA1.Position = Vector3.new(0, -0.5, 0)
        trailA1.Parent = root
        trail = Instance.new("Trail")
        trail.Attachment0 = trailA0
        trail.Attachment1 = trailA1
        trail.Lifetime = 1
        trail.MinLength = 0.1
        trail.Color = ColorSequence.new({
            ColorSequenceKeypoint.new(0, RED),
            ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 100, 100)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255))
        })
        trail.Transparency = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0),
            NumberSequenceKeypoint.new(1, 1)
        })
        trail.LightEmission = 1
        trail.FaceCamera = true
        trail.Parent = root
    else
        if trail then trail:Destroy() trail = nil end
        if trailA0 then trailA0:Destroy() trailA0 = nil end
        if trailA1 then trailA1:Destroy() trailA1 = nil end
    end
end

local function setupAura(state)
    auraEnabled = state
    if state and root then
        if aura then aura:Destroy() end
        if auraLight then auraLight:Destroy() end
        local a0 = Instance.new("Attachment")
        a0.Name = "REDX_AuraA0"
        a0.Position = Vector3.new(0, 1, 0)
        a0.Parent = root
        aura = Instance.new("ParticleEmitter")
        aura.Name = "REDX_Aura"
        aura.Texture = "rbxasset://textures/particles/sparkles_main.dds"
        aura.Color = ColorSequence.new(RED, Color3.fromRGB(255, 80, 80))
        aura.Size = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0.5),
            NumberSequenceKeypoint.new(1, 0)
        })
        aura.Transparency = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0.2),
            NumberSequenceKeypoint.new(1, 1)
        })
        aura.Lifetime = NumberRange.new(0.4, 0.8)
        aura.Rate = 40
        aura.Speed = NumberRange.new(1, 3)
        aura.SpreadAngle = Vector2.new(180, 180)
        aura.LightEmission = 1
        aura.Parent = root
        auraLight = Instance.new("PointLight")
        auraLight.Color = RED
        auraLight.Range = 12
        auraLight.Brightness = 2
        auraLight.Parent = root
    else
        if aura then aura:Destroy() aura = nil end
        if auraLight then auraLight:Destroy() auraLight = nil end
        if root then
            for _, c in ipairs(root:GetChildren()) do
                if c.Name:match("REDX_Aura") then c:Destroy() end
            end
        end
    end
end

local origAmbient, origOutdoor, origBrightness
local function setFullbright(state)
    if state then
        origAmbient = Lighting.Ambient
        origOutdoor = Lighting.OutdoorAmbient
        origBrightness = Lighting.Brightness
        Lighting.Ambient = Color3.fromRGB(180, 180, 180)
        Lighting.OutdoorAmbient = Color3.fromRGB(180, 180, 180)
        Lighting.Brightness = 3
    else
        if origAmbient then Lighting.Ambient = origAmbient end
        if origOutdoor then Lighting.OutdoorAmbient = origOutdoor end
        if origBrightness then Lighting.Brightness = origBrightness end
    end
end

local function setBloom(state)
    local existing = Lighting:FindFirstChild("REDX_Bloom")
    if state then
        if not existing then
            local b = Instance.new("BloomEffect")
            b.Name = "REDX_Bloom"
            b.Intensity = 0.8
            b.Size = 24
            b.Threshold = 0.8
            b.Parent = Lighting
        end
    elseif existing then
        existing:Destroy()
    end
end

local stepConn
local function setRunSparks(state)
    if stepConn then stepConn:Disconnect() stepConn = nil end
    if state then
        local lastStep = 0
        stepConn = RunService.Heartbeat:Connect(function()
            if not root or not root.Parent or not humanoid then return end
            local now = tick()
            if now - lastStep < 0.15 then return end
            local speed = root.Velocity.Magnitude
            if speed > 10 and humanoid.FloorMaterial ~= Enum.Material.Air then
                lastStep = now
                local spark = Instance.new("Part")
                spark.Size = Vector3.new(0.3, 0.3, 0.3)
                spark.Shape = Enum.PartType.Ball
                spark.Material = Enum.Material.Neon
                spark.Color = RED
                spark.CanCollide = false
                spark.Anchored = true
                spark.CFrame = root.CFrame * CFrame.new(0, -2.5, 0)
                spark.Parent = workspace
                TweenService:Create(spark, TweenInfo.new(0.5), {
                    Size = Vector3.new(0, 0, 0),
                    Transparency = 1
                }):Play()
                task.delay(0.55, function() if spark then spark:Destroy() end end)
            end
        end)
    end
end

-- ============================================================
-- UI ЕЛЕМЕНТИ
-- ============================================================

-- ▶ БІЙ
makeFeature(combatPage, "AIMBOT", function(s) aimbotEnabled = s end, function(s)
    makeSelector(s, "ЦІЛЬ", {"Head", "HumanoidRootPart", "Torso"}, aimPart, function(v) aimPart = v end)
    makeSlider(s, "FOV", 30, 500, 150, function(v) fov = v end)
    makeSlider(s, "SMOOTHING", 1, 100, 1, function(v) smoothing = v end)
    makeSimpleToggle(s, "TEAM CHECK", function(v) teamCheck = v end)
    makeSimpleToggle(s, "VISIBLE CHECK", function(v) visibleCheck = v end)
end)

makeFeature(combatPage, "HITBOX EXPANDER", setHitbox, function(s)
    makeSlider(s, "РОЗМІР", 2, 20, 5, function(v)
        hitboxSize = v
    end)
    makeSlider(s, "ПРОЗОРІСТЬ", 0, 9, 7, function(v)
        hitboxTrans = v/10
    end)
end)

-- ▶ РУХ
makeFeature(movementPage, "FLY", function(s)
    if s then startFly() else stopFly() end
end, function(s)
    makeSlider(s, "ШВИДКІСТЬ", 10, 200, 60, function(v)
        flySpeed = v
    end)
    makeSlider(s, "ВЕРТ. ШВИДКІСТЬ", 1, 10, 1, function(v)
        flyVerticalSpeed = v
    end)
end)

makeFeature(movementPage, "BUNNY HOP", function(s) bhopEnabled = s end, function(s)
    makeSlider(s, "ПОТУЖНІСТЬ СТРИБКА", 50, 200, 50, function(v)
        if humanoid then
            humanoid.UseJumpPower = true
            humanoid.JumpPower = v
        end
    end)
end)

makeFeature(movementPage, "SPIN", setSpin, function(s)
    makeSlider(s, "ШВИДКІСТЬ", 1, 30, 10, function(v)
        spinSpeed = v
        if spinVelocity then spinVelocity.AngularVelocity = Vector3.new(0, v, 0) end
    end)
end)

makeFeature(movementPage, "SPEED HACK", function(s)
    speedHackEnabled = s
    if humanoid then
        humanoid.UseJumpPower = true
        humanoid.WalkSpeed = s and currentWalkSpeed or 16
        humanoid.JumpPower = currentJumpPower
    end
end, function(s)
    makeSlider(s, "WALK SPEED", 16, 200, 50, function(v)
        currentWalkSpeed = v
        if speedHackEnabled and humanoid then humanoid.WalkSpeed = v end
    end)
    makeSlider(s, "JUMP POWER", 50, 250, 50, function(v)
        currentJumpPower = v
        if humanoid then
            humanoid.UseJumpPower = true
            humanoid.JumpPower = v
        end
    end)
end)

makeSimpleToggle(movementPage, "INFINITE JUMP", function(s) infiniteJump = s end)

makeFeature(movementPage, "GRAVITY", function(s)
    gravityEnabled = s
    workspace.Gravity = s and gravityValue or normalGravity
end, function(s)
    makeSlider(s, "ГРАВІТАЦІЯ", 0, 196, 30, function(v)
        gravityValue = v
        if gravityEnabled then workspace.Gravity = v end
    end)
end)

makeSimpleToggle(movementPage, "NOCLIP", setNoclip)

-- ▶ ВІЗУАЛ
makeFeature(visualPage, "ESP", function(s)
    espEnabled = s
    updateESP()
end, function(s)
    makeSelector(s, "КОЛІР", {"Червоний", "Зелений", "Синій", "Жовтий"}, "Червоний", function(v)
        espFillColor = v == "Червоний" and RED
            or v == "Зелений" and GREEN
            or v == "Синій" and Color3.fromRGB(80,140,255)
            or Color3.fromRGB(255,220,80)
        if espEnabled then updateESP() end
    end)
    makeSlider(s, "ПРОЗОРІСТЬ", 0, 9, 5, function(v)
        espFillTrans = v/10
        if espEnabled then updateESP() end
    end)
end)

makeFeature(visualPage, "3RD PERSON", setThirdPerson, function(s)
    makeSlider(s, "ДІСТАНЦІЯ", 5, 30, 10, function(v)
        if thirdPersonEnabled then
            player.CameraMinZoomDistance = v
            player.CameraMaxZoomDistance = v
        end
    end)
end)

makeSimpleToggle(visualPage, "NIGHT MODE", setNightMode)

makeFeature(visualPage, "TRAIL", setupTrail, function(s)
    makeSlider(s, "ДОВЖИНА", 1, 5, 1, function(v)
        if trail then trail.Lifetime = v end
    end)
end)

makeFeature(visualPage, "AURA", setupAura, function(s)
    makeSlider(s, "ІНТЕНСИВНІСТЬ", 5, 100, 40, function(v)
        if aura then aura.Rate = v end
    end)
    makeSlider(s, "РАДІУС СВІТЛА", 1, 30, 12, function(v)
        if auraLight then auraLight.Range = v end
    end)
end)

makeSimpleToggle(visualPage, "RUN SPARKS", setRunSparks)
makeSimpleToggle(visualPage, "FULLBRIGHT", setFullbright)
makeSimpleToggle(visualPage, "BLOOM", setBloom)

-- ▶ ІНШЕ
makeFeature(miscPage, "ANTI AFK", setAntiAFK)

makeSimpleToggle(miscPage, "AUTO RESPAWN", function(s)
    autoRespawn = s
end)

local function hookRespawn(char)
    local hum = char:WaitForChild("Humanoid")
    hum.Died:Connect(function()
        if autoRespawn then
            task.wait(0.5)
            pcall(function() player:LoadCharacter() end)
        end
    end)
end
if player.Character then hookRespawn(player.Character) end
player.CharacterAdded:Connect(hookRespawn)

makeAction(miscPage, "TP TO SPAWN", function()
    if root then
        local sp = workspace:FindFirstChildOfClass("SpawnLocation")
        if sp then root.CFrame = sp.CFrame + Vector3.new(0,5,0) end
    end
end)

makeAction(miscPage, "TP TO NEAREST PLAYER", function()
    local nearest, dist = nil, math.huge
    for _, v in ipairs(Players:GetPlayers()) do
        if v ~= player and v.Character and v.Character:FindFirstChild("HumanoidRootPart") and root then
            local d = (v.Character.HumanoidRootPart.Position - root.Position).Magnitude
            if d < dist then dist = d nearest = v end
        end
    end
    if nearest and nearest.Character and root then
        root.CFrame = nearest.Character.HumanoidRootPart.CFrame + Vector3.new(0,3,0)
    end
end)

makeAction(miscPage, "RESPAWN", function()
    if humanoid then humanoid.Health = 0 end
end)

makeAction(miscPage, "REJOIN SERVER", function()
    local placeId = game.PlaceId
    local jobId = game.JobId
    player:Kick("REDX HUB - Rejoining...")
    task.delay(1, function()
        TeleportService:TeleportToPlaceInstance(placeId, jobId, player)
    end)
end)

makeAction(miscPage, "LEAVE SERVER", function()
    player:Kick("REDX HUB - You left!")
end)

-- ============================================================
-- RESPAWN HANDLER (FIXED)
-- ============================================================
player.CharacterAdded:Connect(function(newCharacter)
    character = newCharacter
    humanoid = newCharacter:WaitForChild("Humanoid")
    root = newCharacter:WaitForChild("HumanoidRootPart")

    humanoid.PlatformStand = false
    local animate = newCharacter:FindFirstChild("Animate")
    if animate then animate.Disabled = false end

    noclip = false
    table.clear(originalCollision)
    local wasFlying = flying
    stopFly()

    task.wait(.3)

    humanoid.UseJumpPower = true
    humanoid.JumpPower = currentJumpPower
    humanoid.WalkSpeed = speedHackEnabled and currentWalkSpeed or 16
    if gravityEnabled then workspace.Gravity = gravityValue end
    if spinEnabled then setSpin(true) end
    if espEnabled then updateESP() end

    -- Перестворюємо візуальні ефекти
    if trailEnabled then task.wait(0.2) setupTrail(true) end
    if auraEnabled then task.wait(0.2) setupAura(true) end

    if wasFlying then
        task.delay(1, function()
            if flying then return end
            startFly()
        end)
    end
end)

-- INFINITE JUMP
UIS.JumpRequest:Connect(function()
    if infiniteJump and humanoid and humanoid.Parent then
        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

print("REDX HUB V7 MOBILE LOADED! ✔")
