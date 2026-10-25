-- FINAL OPTIMIZED COMPACT BEANZ HUB (STICKY AIM INTEGRATION)
local P = game:GetService("Players")
local LP = P.LocalPlayer
local Cam = workspace.CurrentCamera
local RS = game:GetService("RunService")
local UIS = game:GetService("UserInputService")

-- CONFIGURATION SYSTEM STATE VALUES
_G.AimbotActive = false
_G.AimMode = "Hold"
_G.TargetBone = "Head"
_G.SmoothnessValue = 0
_G.WallCheckActive = false
_G.TeamCheckActive = false
_G.PredictionValue = 0
_G.StickyAimActive = false
_G.FovVisibleActive = true
_G.FovSizeValue = 2
_G.EspActive = false
_G.HoldBind = "MouseButton2" -- Default PC Lock Key (Right Click)
_G.UiToggleBind = "Insert"    -- Default UI Hide Key

-- STICKY TARGET MEMORY BUFFER
local StickyTargetPlayer = nil

if LP.PlayerGui:FindFirstChild("BeanzHub") then 
    LP.PlayerGui.BeanzHub:Destroy() 
end

local Gui = Instance.new("ScreenGui", LP.PlayerGui) 
Gui.Name = "BeanzHub" 
Gui.ResetOnSpawn = false

-- ULTRA-COMPACT TRANSPARENT MAIN BACKPLATE FRAME
local MainFrame = Instance.new("Frame", Gui)
MainFrame.Size = UDim2.new(0, 310, 0, 240)
MainFrame.Position = UDim2.new(0.5, -155, 0.5, -120)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BackgroundTransparency = 0.55 -- Semi-Transparent Light Glass
MainFrame.Active = true
MainFrame.Draggable = true
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 8)

local MasterOutline = Instance.new("UIStroke", MainFrame)
MasterOutline.Color = Color3.fromRGB(255, 255, 255) -- Crisp White Border
MasterOutline.Thickness = 1.2

-- TITLE & STATIC TABS
local HubTitle = Instance.new("TextLabel", MainFrame)
HubTitle.Size = UDim2.new(1, -20, 0, 24)
HubTitle.Position = UDim2.new(0, 12, 0, 4)
HubTitle.BackgroundTransparency = 1
HubTitle.Text = "Beanz Hub"
HubTitle.TextColor3 = Color3.fromRGB(180, 180, 180)
HubTitle.Font = Enum.Font.SourceSansBold
HubTitle.TextSize = 18
HubTitle.TextXAlignment = Enum.TextXAlignment.Left

local AimTabBtn = Instance.new("TextButton", MainFrame)
AimTabBtn.Size = UDim2.new(0, 50, 0, 20)
AimTabBtn.Position = UDim2.new(0, 12, 0, 26)
AimTabBtn.BackgroundTransparency = 1
AimTabBtn.Text = "Aimbot"
AimTabBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
AimTabBtn.Font = Enum.Font.SourceSansBold
AimTabBtn.TextSize = 14
AimTabBtn.TextXAlignment = Enum.TextXAlignment.Left

local EspTabBtn = Instance.new("TextButton", MainFrame)
EspTabBtn.Size = UDim2.new(0, 40, 0, 20)
EspTabBtn.Position = UDim2.new(0, 70, 0, 26)
EspTabBtn.BackgroundTransparency = 1
EspTabBtn.Text = "Esp"
EspTabBtn.TextColor3 = Color3.fromRGB(130, 130, 130)
EspTabBtn.Font = Enum.Font.SourceSansBold
EspTabBtn.TextSize = 14
EspTabBtn.TextXAlignment = Enum.TextXAlignment.Left

-- SUB CANVAS PANELS
local AimbotCanvas = Instance.new("Frame", MainFrame)
AimbotCanvas.Size = UDim2.new(1, 0, 1, -50)
AimbotCanvas.Position = UDim2.new(0, 0, 0, 50)
AimbotCanvas.BackgroundTransparency = 1

local EspCanvas = Instance.new("Frame", MainFrame)
EspCanvas.Size = UDim2.new(1, 0, 1, -50)
EspCanvas.Position = UDim2.new(0, 0, 0, 50)
EspCanvas.BackgroundTransparency = 1
EspCanvas.Visible = false

AimTabBtn.MouseButton1Click:Connect(function()
    AimbotCanvas.Visible = true
    EspCanvas.Visible = false
    AimTabBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    EspTabBtn.TextColor3 = Color3.fromRGB(130, 130, 130)
end)

EspTabBtn.MouseButton1Click:Connect(function()
    AimbotCanvas.Visible = false
    EspCanvas.Visible = true
    AimTabBtn.TextColor3 = Color3.fromRGB(130, 130, 130)
    EspTabBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
end)

-- COMPACT LAYOUT FACTORY HELPERS
local function CreateSectionBox(parent, title, size, pos)
    local box = Instance.new("Frame", parent)
    box.Size = size
    box.Position = pos
    box.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
    box.BackgroundTransparency = 0.6
    Instance.new("UICorner", box).CornerRadius = UDim.new(0, 4)
    local stroke = Instance.new("UIStroke", box)
    stroke.Color = Color3.fromRGB(50, 80, 220) -- Accent Blue Borders
    stroke.Thickness = 1.2
    
    local lbl = Instance.new("TextLabel", parent)
    lbl.Size = UDim2.new(0, 60, 0, 14)
    lbl.Position = UDim2.new(pos.X.Scale, pos.X.Offset + 8, pos.Y.Scale, pos.Y.Offset - 7)
    lbl.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    lbl.BackgroundTransparency = 0.2
    lbl.Text = " " .. title .. " "
    lbl.TextColor3 = Color3.fromRGB(70, 110, 255)
    lbl.Font = Enum.Font.SourceSansBold
    lbl.TextSize = 11
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    return box
end

local function CreateCapsuleToggle(parent, text, x, y, startState, callback)
    local pill = Instance.new("Frame", parent)
    pill.Size = UDim2.new(0, 20, 0, 11)
    pill.Position = UDim2.new(0, x, 0, y + 3)
    pill.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
    Instance.new("UICorner", pill).CornerRadius = UDim.new(1, 0)
    local stroke = Instance.new("UIStroke", pill)
    stroke.Color = Color3.fromRGB(60, 60, 60)
    stroke.Thickness = 1

    local dot = Instance.new("Frame", pill)
    dot.Size = UDim2.new(0, 14, 0, 7)
    dot.Position = UDim2.new(0.5, -7, 0.5, -3)
    dot.BackgroundColor3 = Color3.fromRGB(0, 80, 255)
    Instance.new("UICorner", dot).CornerRadius = UDim.new(1, 0)
    dot.BackgroundTransparency = startState and 0 or 1

    local btn = Instance.new("TextButton", pill)
    btn.Size = UDim2.new(1, 0, 1, 0)
    btn.BackgroundTransparency = 1
    btn.Text = ""

    local lbl = Instance.new("TextLabel", parent)
    lbl.Size = UDim2.new(0.7, 0, 0, 18)
    lbl.Position = UDim2.new(0, x + 28, 0, y)
    lbl.BackgroundTransparency = 1
    lbl.Text = text
    lbl.TextColor3 = Color3.fromRGB(200, 200, 200)
    lbl.TextSize = 12
    lbl.Font = Enum.Font.SourceSans
    lbl.TextXAlignment = Enum.TextXAlignment.Left

    local state = startState
    btn.MouseButton1Click:Connect(function()
        state = not state
        dot.BackgroundTransparency = state and 0 or 1
        callback(state)
    end)
    return function(force)
        state = force
        dot.BackgroundTransparency = state and 0 or 1
    end
end

local function CreateDropdown(parent, labelText, x, y, options, currentDef, callback)
    local lbl = Instance.new("TextLabel", parent)
    lbl.Size = UDim2.new(0.4, 0, 0, 18)
    lbl.Position = UDim2.new(0, x, 0, y)
    lbl.BackgroundTransparency = 1
    lbl.Text = labelText
    lbl.TextColor3 = Color3.fromRGB(190, 190, 190)
    lbl.Font = Enum.Font.SourceSans
    lbl.TextSize = 12
    lbl.TextXAlignment = Enum.TextXAlignment.Left

    local db = Instance.new("TextButton", parent)
    db.Size = UDim2.new(0, 75, 0, 16)
    db.Position = UDim2.new(0, x + 50, 0, y + 1)
    db.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    db.Text = " " .. currentDef
    db.Font = Enum.Font.SourceSans
    db.TextColor3 = Color3.fromRGB(210, 210, 210)
    db.TextSize = 11
    db.TextXAlignment = Enum.TextXAlignment.Left
    Instance.new("UICorner", db).CornerRadius = UDim.new(0, 4)
    local s = Instance.new("UIStroke", db)
    s.Color = Color3.fromRGB(80, 80, 80)

    local arr = Instance.new("TextLabel", db)
    arr.Size = UDim2.new(0, 12, 1, 0)
    arr.Position = UDim2.new(1, -14, 0, 0)
    arr.BackgroundTransparency = 1
    arr.Text = "▼"
    arr.TextColor3 = Color3.fromRGB(120, 120, 120)
    arr.TextSize = 8

    local list = Instance.new("Frame", Gui)
    list.Size = UDim2.new(0, 75, 0, #options * 18)
    list.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    list.Visible = false
    Instance.new("UICorner", list).CornerRadius = UDim.new(0, 4)
    local ls = Instance.new("UIStroke", list)
    ls.Color = Color3.fromRGB(70, 70, 70)

    for i, opt in ipairs(options) do
        local ob = Instance.new("TextButton", list)
        ob.Size = UDim2.new(1, 0, 0, 18)
        ob.Position = UDim2.new(0, 0, 0, (i - 1) * 18)
        ob.BackgroundTransparency = 1
        ob.Text = " " .. opt
        ob.TextColor3 = Color3.fromRGB(190, 190, 190)
        ob.Font = Enum.Font.SourceSans
        ob.TextSize = 11
        ob.TextXAlignment = Enum.TextXAlignment.Left
        ob.MouseButton1Click:Connect(function()
            db.Text = " " .. opt
            list.Visible = false
            callback(opt)
        end)
    end

    db.MouseButton1Click:Connect(function()
        if list.Visible then list.Visible = false else
            list.Position = UDim2.new(0, db.AbsolutePosition.X, 0, db.AbsolutePosition.Y + 18)
            list.Visible = true
        end
    end)
end

local function CreatePillInput(parent, label, def, x, y, callback)
    local lbl = Instance.new("TextLabel", parent)
    lbl.Size = UDim2.new(0, 60, 0, 18)
    lbl.Position = UDim2.new(0, x, 0, y)
    lbl.BackgroundTransparency = 1
    lbl.Text = label
    lbl.TextColor3 = Color3.fromRGB(200, 200, 200)
    lbl.Font = Enum.Font.SourceSans
    lbl.TextSize = 12
    lbl.TextXAlignment = Enum.TextXAlignment.Left

    local box = Instance.new("TextBox", parent)
    box.Size = UDim2.new(0, 40, 0, 16)
    box.Position = UDim2.new(0, x + 85, 0, y + 1)
    box.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    box.TextColor3 = Color3.fromRGB(220, 220, 220)
    box.Text = tostring(def)
    box.Font = Enum.Font.SourceSansBold
    box.TextSize = 11
    box.ClearTextOnFocus = false
    Instance.new("UICorner", box).CornerRadius = UDim.new(0, 6)
    local s = Instance.new("UIStroke", box)
    s.Color = Color3.fromRGB(80, 80, 80)
    box.FocusLost:Connect(function() callback(box.Text) end)
end

-- ====================================================================
-- GENERATING OBJECT VISUAL TABS FOOTPRINT
-- ====================================================================
local ValBox = CreateSectionBox(AimbotCanvas, "Values", UDim2.new(0, 140, 0, 180), UDim2.new(0, 8, 0, 5))
local ChkBox = CreateSectionBox(AimbotCanvas, "Checks", UDim2.new(0, 145, 0, 105), UDim2.new(0, 155, 0, 5))
local FovBox = CreateSectionBox(AimbotCanvas, "FOV", UDim2.new(0, 145, 0, 70), UDim2.new(0, 155, 0, 115))

-- VALUES PAGE MODULES
local masterToggle = CreateCapsuleToggle(ValBox, "Enable Aimbot", 8, 10, _G.AimbotActive, function(s) _G.AimbotActive = s end)
CreateDropdown(ValBox, "Mode", 8, 30, {"Hold", "Toggle"}, "Hold", function(o) _G.AimMode = o end)
CreateDropdown(ValBox, "Target", 8, 52, {"Head", "Torso"}, "Head", function(o) _G.TargetBone = o == "Head" and "Head" or "HumanoidRootPart" end)
CreatePillInput(ValBox, "Smoothness", _G.SmoothnessValue, 8, 75, function(t) local n = tonumber(t) if n then _G.SmoothnessValue = math.clamp(n, 0, 10) end end)
CreatePillInput(ValBox, "Lock Button", "MB2", 8, 98, function(t) _G.HoldBind = t end)
CreatePillInput(ValBox, "UI Keybind", "Insert", 8, 121, function(t) _G.UiToggleBind = t end)

-- CHECKS PAGE MODULES
CreateCapsuleToggle(ChkBox, "Wall Check", 8, 10, _G.WallCheckActive, function(s) _G.WallCheckActive = s end)
CreateCapsuleToggle(ChkBox, "Team Check", 8, 28, _G.TeamCheckActive, function(s) _G.TeamCheckActive = s end)
CreatePillInput(ChkBox, "Prediction", _G.PredictionValue, 8, 46, function(t) local n = tonumber(t) if n then _G.PredictionValue = math.clamp(n, 0, 10) * 0.1 end end)
CreateCapsuleToggle(ChkBox, "Sticky Aim", 8, 70, _G.StickyAimActive, function(s) 
    _G.StickyAimActive = s 
    if not s then StickyTargetPlayer = nil end -- Clear sticky target cache when turned off
end)

-- FOV PAGE MODULES
CreateCapsuleToggle(FovBox, "FOV Visible", 8, 10, _G.FovVisibleActive, function(s) _G.FovVisibleActive = s end)
CreatePillInput(FovBox, "FOV Size", 2, 8, 30, function(t) local n = tonumber(t) if n then _G.FovSizeValue = math.clamp(n, 0, 10) end end)

-- VISUALS TABS FOOTPRINT
local EspValBox = CreateSectionBox(EspCanvas, "Values", UDim2.new(0, 140, 0, 180), UDim2.new(0, 8, 0, 5))
CreateCapsuleToggle(EspValBox, "Enable ESP", 8, 10, _G.EspActive, function(s) _G.EspActive = s end)

local OverlayRing = Instance.new("Frame", Gui) 
OverlayRing.AnchorPoint = Vector2.new(0.5, 0.5)
OverlayRing.Position = UDim2.new(0.5, 0, 0.5, 0)
OverlayRing.BackgroundTransparency = 1
Instance.new("UICorner", OverlayRing).CornerRadius = UDim.new(1, 0)
local RingStroke = Instance.new("UIStroke", OverlayRing) 
RingStroke.Color = Color3.fromRGB(50, 80, 220)
RingStroke.Thickness = 1

-- MOUSE & HARDWARE INTERFACE CAPTURE ROUTINES
local function CheckKeyInput(inputObj, targetString)
    if targetString == "MB1" or targetString == "MouseButton1" then
        return inputObj.UserInputType == Enum.UserInputType.MouseButton1
    elseif targetString == "MB2" or targetString == "MouseButton2" then
        return inputObj.UserInputType == Enum.UserInputType.MouseButton2
    else
        return inputObj.KeyCode == Enum.KeyCode[targetString]
    end
end

UIS.InputBegan:Connect(function(i, gp)
    if i.KeyCode.Name == _G.UiToggleBind then
        MainFrame.Visible = not MainFrame.Visible
    end
    if gp then return end
    if _G.AimMode == "Hold" and CheckKeyInput(i, _G.HoldBind) then
        _G.AimbotActive = true
        masterToggle(true)
    elseif _G.AimMode == "Toggle" and CheckKeyInput(i, _G.HoldBind) then
        _G.AimbotActive = not _G.AimbotActive
        masterToggle(_G.AimbotActive)
        if not _G.AimbotActive then StickyTargetPlayer = nil end -- Clear sticky target cache on unlock
    end
end)

UIS.InputEnded:Connect(function(i)
    if _G.AimMode == "Hold" and CheckKeyInput(i, _G.HoldBind) then
        _G.AimbotActive = false
        masterToggle(false)
        StickyTargetPlayer = nil -- Clear sticky target cache when letting go of hold key
    end
end)

local function ComputeLOS(part)
    if not _G.WallCheckActive then return true end
    local params = RaycastParams.new()
    params.FilterType = Enum.RaycastFilterType.Exclude
    params.FilterDescendantsInstances = {LP.Character}
    local res = workspace:Raycast(Cam.CFrame.Position, part.Position - Cam.CFrame.Position, params)
    return not res or res.Instance:IsDescendantOf(part.Parent)
end

-- TARGET REQUISITION ENGINE WITH STICKY AIM MUTEX INTERPRETATION
local function FetchTarget(radius) 
    -- Sticky Aim Mutex Check Override: If locked onto a target and sticky option is true, stay glued completely ignoring obstacles
    if _G.StickyAimActive and StickyTargetPlayer and StickyTargetPlayer.Character and StickyTargetPlayer.Character:FindFirstChild(_G.TargetBone) and StickyTargetPlayer.Character:FindFirstChild("Humanoid") and StickyTargetPlayer.Character.Humanoid.Health > 0 then
        return StickyTargetPlayer
    end
    
    local target = nil
    local limit = radius
    
    for _, v in pairs(P:GetPlayers()) do 
        if v ~= LP and v.Character and v.Character:FindFirstChild(_G.TargetBone) and v.Character:FindFirstChild("Humanoid") and v.Character.Humanoid.Health > 0 then 
            if _G.TeamCheckActive and v.Team == LP.Team then continue end 
            
            local node = v.Character[_G.TargetBone]
            local posVec, visible = Cam:WorldToViewportPoint(node.Position) 
            
            if visible and ComputeLOS(node) then 
                local center = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
                local dist = (Vector2.new(posVec.X, posVec.Y) - center).Magnitude 
                if dist < limit then 
                    limit = dist
                    target = v 
                end 
            end 
        end 
    end 
    
    -- Save targeted player into memory block if sticky aim is checked active
    if _G.StickyAimActive and target then
        StickyTargetPlayer = target
    end
    
    return target 
end

RS.RenderStepped:Connect(function() 
    local activeRadius = _G.FovSizeValue * 45
    OverlayRing.Visible = _G.FovVisibleActive
    OverlayRing.Size = UDim2.new(0, activeRadius * 2, 0, activeRadius * 2) 
    
    if _G.AimbotActive then
        local matched = FetchTarget(activeRadius) 
        if matched and matched.Character and matched.Character:FindFirstChild(_G.TargetBone) then 
            local coords = matched.Character[_G.TargetBone].Position 
            if matched.Character:FindFirstChild("HumanoidRootPart") and _G.PredictionValue > 0 then 
                coords = coords + (matched.Character.HumanoidRootPart.Velocity * _G.PredictionValue) 
            end 
            if _G.SmoothnessValue == 0 then
                Cam.CFrame = CFrame.new(Cam.CFrame.Position, coords)
            else
                Cam.CFrame = Cam.CFrame:Lerp(CFrame.new(Cam.CFrame.Position, coords), 1 / _G.SmoothnessValue) 
            end
        else
            -- If sticky target died or vanished while unlocked, flush memory buffer
            StickyTargetPlayer = nil
        end 
    end
    
    -- STABLE HIGHLIGHT ENGINE CONSOLE THREAD
    for _, v in pairs(P:GetPlayers()) do 
        if v ~= LP and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then 
            local actor = v.Character 
            local element = actor:FindFirstChild("ESPH") 
            if _G.EspActive and actor:FindFirstChild("Humanoid") and actor.Humanoid.Health > 0 then 
                local teamCol = v.Team and v.TeamColor.Color or Color3.fromRGB(255, 255, 255) 
                if not element then 
                    local glow = Instance.new("Highlight") 
                    glow.Name = "ESPH" 
                    glow.FillTransparency = 0.6
                    glow.OutlineTransparency = 0.2
                    glow.FillColor = teamCol
                    glow.OutlineColor = teamCol 
                    glow.Parent = actor
                else 
                    element.FillColor = teamCol
                    element.OutlineColor = teamCol 
                end 
            else 
                if element then element:Destroy() end 
            end 
        end 
    end 
end)
