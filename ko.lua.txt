--[[
    🍪 OREO HUB - Full Script
    Owner: longne12345666 | Dev: Sobi
    Discord: https://discord.gg/HMfsMcUWH2
]]

if not game:IsLoaded() then game.Loaded:Wait() end

local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local ProximityPromptService = game:GetService("ProximityPromptService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

for _, g in ipairs(PlayerGui:GetChildren()) do
    if g.Name == "OreoHubUI" or g.Name == "OreoHubToggle" then g:Destroy() end
end

-- Modules
local EggCmds, Network, NM, Constants, Bases, Treadmills, Trails, Save
local PlotsNet, TreadmillsNet, TrailsNet
pcall(function() EggCmds = require(ReplicatedStorage.Library.Client.EggCmds) end)
pcall(function() Network = require(ReplicatedStorage.Library.Client.Network) end)
pcall(function() Constants = require(ReplicatedStorage.Library.Globals.Constants) end)
pcall(function() NM = (Network and Network.NET_MAP) or (Constants and Constants.NETWORK_MAP) end)
pcall(function() Bases = require(ReplicatedStorage.Directory.Bases) end)
pcall(function() Treadmills = require(ReplicatedStorage.Directory.Treadmills) end)
pcall(function() Trails = require(ReplicatedStorage.Directory.Trails) end)
pcall(function() Save = require(ReplicatedStorage.Library.Client.Save) end)
pcall(function() PlotsNet = Constants and Constants.NETWORK_MAP and Constants.NETWORK_MAP.Plots end)
pcall(function() TreadmillsNet = Constants and Constants.NETWORK_MAP and Constants.NETWORK_MAP.Treadmills end)
pcall(function() TrailsNet = Constants and Constants.NETWORK_MAP and Constants.NETWORK_MAP.Trails end)

ProximityPromptService.PromptShown:Connect(function(p) p.HoldDuration = 0 end)

local C = {
    Bg = Color3.fromRGB(12, 12, 18), BgLight = Color3.fromRGB(20, 20, 28),
    Card = Color3.fromRGB(24, 24, 34), CardHover = Color3.fromRGB(34, 34, 48),
    Border = Color3.fromRGB(45, 45, 62), Accent = Color3.fromRGB(180, 140, 255),
    AccentGlow = Color3.fromRGB(220, 190, 255), AccentDim = Color3.fromRGB(90, 60, 150),
    Text = Color3.fromRGB(235, 235, 245), TextDim = Color3.fromRGB(150, 150, 175),
    Discord = Color3.fromRGB(88, 101, 242), Snow = Color3.fromRGB(240, 248, 255),
    Danger = Color3.fromRGB(240, 90, 90), Success = Color3.fromRGB(90, 220, 140),
}

local DISCORD_LINK = "https://discord.gg/HMfsMcUWH2"
local OWNER = "vietnamesee25"
local DEV = "github : vietnamese25"

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "OreoHubUI"; ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = true; ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = PlayerGui

local ToggleGui = Instance.new("ScreenGui")
ToggleGui.Name = "OreoHubToggle"; ToggleGui.ResetOnSpawn = false
ToggleGui.IgnoreGuiInset = true; ToggleGui.Parent = PlayerGui

local Main = Instance.new("Frame")
Main.Size = UDim2.new(0, 560, 0, 420)
Main.Position = UDim2.new(0.5, -280, 0.5, -210)
Main.BackgroundColor3 = C.Bg; Main.BackgroundTransparency = 0.05
Main.BorderSizePixel = 0; Main.Active = true; Main.Draggable = true
Main.ClipsDescendants = true; Main.Parent = ScreenGui
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 16)

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = C.Accent; MainStroke.Thickness = 1.5; MainStroke.Transparency = 0.3
MainStroke.Parent = Main

task.spawn(function()
    while Main.Parent do
        TweenService:Create(MainStroke, TweenInfo.new(1.8, Enum.EasingStyle.Sine), {Transparency = 0.75}):Play()
        task.wait(1.8)
        if not Main.Parent then break end
        TweenService:Create(MainStroke, TweenInfo.new(1.8, Enum.EasingStyle.Sine), {Transparency = 0.15}):Play()
        task.wait(1.8)
    end
end)

-- Snow
local SnowLayer = Instance.new("Frame")
SnowLayer.Size = UDim2.new(1, 0, 1, 0); SnowLayer.BackgroundTransparency = 1
SnowLayer.BorderSizePixel = 0; SnowLayer.ZIndex = 100; SnowLayer.Active = false
SnowLayer.Parent = Main

local snowEnabled = true
local function spawnSnowflake()
    if not SnowLayer.Parent or not snowEnabled then return end
    local size = math.random(2, 5)
    local flake = Instance.new("Frame")
    flake.Size = UDim2.new(0, size, 0, size)
    flake.Position = UDim2.new(math.random(), 0, -0.1, 0)
    flake.BackgroundColor3 = C.Snow
    flake.BackgroundTransparency = math.random(40, 75) / 100
    flake.BorderSizePixel = 0; flake.ZIndex = 100; flake.Parent = SnowLayer
    Instance.new("UICorner", flake).CornerRadius = UDim.new(1, 0)
    local dur = math.random(6, 13)
    local endX = math.clamp(flake.Position.X.Scale + (math.random(-30, 30)) / 1000, 0, 1)
    TweenService:Create(flake, TweenInfo.new(dur, Enum.EasingStyle.Linear), {Position = UDim2.new(endX, 0, 1.1, 0)}):Play()
    task.delay(dur + 0.1, function() if flake then flake:Destroy() end end)
end
task.spawn(function()
    while Main.Parent do
        if snowEnabled then spawnSnowflake() end
        task.wait(math.random(6, 18) / 100)
    end
end)

-- Header
local Header = Instance.new("Frame")
Header.Size = UDim2.new(1, 0, 0, 60); Header.BackgroundColor3 = C.BgLight
Header.BorderSizePixel = 0; Header.ZIndex = 5; Header.Parent = Main
Instance.new("UICorner", Header).CornerRadius = UDim.new(0, 16)

local HeaderMask = Instance.new("Frame")
HeaderMask.Size = UDim2.new(1, 0, 0.5, 0); HeaderMask.Position = UDim2.new(0, 0, 0.5, 0)
HeaderMask.BackgroundColor3 = C.BgLight; HeaderMask.BorderSizePixel = 0
HeaderMask.ZIndex = 5; HeaderMask.Parent = Header

local Logo = Instance.new("Frame")
Logo.Size = UDim2.new(0, 40, 0, 40); Logo.Position = UDim2.new(0, 14, 0.5, -20)
Logo.BackgroundColor3 = C.Accent; Logo.BorderSizePixel = 0
Logo.ZIndex = 6; Logo.Parent = Header
Instance.new("UICorner", Logo).CornerRadius = UDim.new(1, 0)

local LogoLabel = Instance.new("TextLabel")
LogoLabel.Size = UDim2.new(1, 0, 1, 0); LogoLabel.BackgroundTransparency = 1
LogoLabel.Text = "OH"; LogoLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
LogoLabel.TextSize = 16; LogoLabel.Font = Enum.Font.GothamBold
LogoLabel.ZIndex = 7; LogoLabel.Parent = Logo

task.spawn(function()
    while Logo.Parent do
        TweenService:Create(Logo, TweenInfo.new(1.2, Enum.EasingStyle.Sine), {BackgroundColor3 = C.AccentGlow}):Play()
        task.wait(1.2)
        if not Logo.Parent then break end
        TweenService:Create(Logo, TweenInfo.new(1.2, Enum.EasingStyle.Sine), {BackgroundColor3 = C.Accent}):Play()
        task.wait(1.2)
    end
end)

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -180, 1, 0); Title.Position = UDim2.new(0, 64, 0, 0)
Title.BackgroundTransparency = 1; Title.Text = "Pham Longg"
Title.TextColor3 = C.Text; Title.TextSize = 18
Title.Font = Enum.Font.GothamBold; Title.TextXAlignment = Enum.TextXAlignment.Left
Title.ZIndex = 6; Title.Parent = Header

local TitleGradient = Instance.new("UIGradient")
TitleGradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, C.Accent),
    ColorSequenceKeypoint.new(0.5, C.AccentGlow),
    ColorSequenceKeypoint.new(1, C.Accent),
})
TitleGradient.Parent = Title

task.spawn(function()
    while Title.Parent do
        TweenService:Create(TitleGradient, TweenInfo.new(3, Enum.EasingStyle.Linear), {Offset = Vector2.new(1, 0)}):Play()
        task.wait(3)
        if not Title.Parent then break end
        TitleGradient.Offset = Vector2.new(-1, 0)
    end
end)

local MinBtn = Instance.new("TextButton")
MinBtn.Size = UDim2.new(0, 36, 0, 36); MinBtn.Position = UDim2.new(1, -90, 0.5, -18)
MinBtn.BackgroundColor3 = C.Card; MinBtn.BorderSizePixel = 0
MinBtn.Text = "−"; MinBtn.TextColor3 = C.TextDim; MinBtn.TextSize = 22
MinBtn.Font = Enum.Font.GothamBold; MinBtn.AutoButtonColor = false
MinBtn.ZIndex = 6; MinBtn.Parent = Header
Instance.new("UICorner", MinBtn).CornerRadius = UDim.new(0, 10)
MinBtn.MouseEnter:Connect(function() TweenService:Create(MinBtn, TweenInfo.new(0.2), {BackgroundColor3 = C.Accent, TextColor3 = Color3.fromRGB(255,255,255)}):Play() end)
MinBtn.MouseLeave:Connect(function() TweenService:Create(MinBtn, TweenInfo.new(0.2), {BackgroundColor3 = C.Card, TextColor3 = C.TextDim}):Play() end)

local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 36, 0, 36); CloseBtn.Position = UDim2.new(1, -48, 0.5, -18)
CloseBtn.BackgroundColor3 = C.Card; CloseBtn.BorderSizePixel = 0
CloseBtn.Text = "X"; CloseBtn.TextColor3 = C.TextDim; CloseBtn.TextSize = 16
CloseBtn.Font = Enum.Font.GothamBold; CloseBtn.AutoButtonColor = false
CloseBtn.ZIndex = 6; CloseBtn.Parent = Header
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(0, 10)
CloseBtn.MouseEnter:Connect(function() TweenService:Create(CloseBtn, TweenInfo.new(0.2), {BackgroundColor3 = C.Danger, TextColor3 = Color3.fromRGB(255,255,255)}):Play() end)
CloseBtn.MouseLeave:Connect(function() TweenService:Create(CloseBtn, TweenInfo.new(0.2), {BackgroundColor3 = C.Card, TextColor3 = C.TextDim}):Play() end)
CloseBtn.MouseButton1Click:Connect(function() ScreenGui.Enabled = false end)

local Body = Instance.new("Frame")
Body.Name = "Body"
Body.Size = UDim2.new(1, 0, 1, -60); Body.Position = UDim2.new(0, 0, 0, 60)
Body.BackgroundTransparency = 1; Body.ZIndex = 6
Body.ClipsDescendants = true; Body.Parent = Main

local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.new(0, 140, 1, 0); Sidebar.BackgroundTransparency = 1
Sidebar.ZIndex = 6; Sidebar.Parent = Body

local Indicator = Instance.new("Frame")
Indicator.Size = UDim2.new(0, 3, 0, 38); Indicator.Position = UDim2.new(0, 0, 0, 12)
Indicator.BackgroundColor3 = C.Accent; Indicator.BorderSizePixel = 0
Indicator.ZIndex = 7; Indicator.Parent = Sidebar
Instance.new("UICorner", Indicator).CornerRadius = UDim.new(1, 0)
local IndicatorGlow = Instance.new("UIStroke")
IndicatorGlow.Color = C.AccentGlow; IndicatorGlow.Thickness = 3
IndicatorGlow.Transparency = 0.6; IndicatorGlow.Parent = Indicator

local Content = Instance.new("Frame")
Content.Size = UDim2.new(1, -140, 1, -10); Content.Position = UDim2.new(0, 140, 0, 10)
Content.BackgroundTransparency = 1; Content.ZIndex = 6; Content.Parent = Body

local minimized = false
local function setMinimized(state)
    minimized = state
    if state then
        TweenService:Create(Main, TweenInfo.new(0.3, Enum.EasingStyle.Quint), {Size = UDim2.new(0, 560, 0, 60)}):Play()
        Body.Visible = false; MinBtn.Text = "+"
    else
        TweenService:Create(Main, TweenInfo.new(0.3, Enum.EasingStyle.Quint), {Size = UDim2.new(0, 560, 0, 420)}):Play()
        task.wait(0.15); Body.Visible = true; MinBtn.Text = "−"
    end
end
MinBtn.MouseButton1Click:Connect(function() setMinimized(not minimized) end)

local tabs, pages, activeTab = {}, {}, nil

local function createTab(id, label, order)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -20, 0, 38); btn.Position = UDim2.new(0, 10, 0, 12 + order * 46)
    btn.BackgroundColor3 = C.Card; btn.BackgroundTransparency = 1
    btn.BorderSizePixel = 0; btn.Text = label; btn.TextColor3 = C.TextDim
    btn.TextSize = 14; btn.Font = Enum.Font.GothamMedium
    btn.AutoButtonColor = false; btn.ZIndex = 7; btn.Parent = Sidebar
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 10)

    local page = Instance.new("ScrollingFrame")
    page.Size = UDim2.new(1, 0, 1, 0); page.BackgroundTransparency = 1
    page.BorderSizePixel = 0; page.ScrollBarThickness = 4
    page.ScrollBarImageColor3 = C.Accent; page.ScrollBarImageTransparency = 0.3
    page.CanvasSize = UDim2.new(0, 0, 0, 900); page.Visible = false
    page.ZIndex = 7; page.Parent = Content
    local layout = Instance.new("UIListLayout"); layout.Padding = UDim.new(0, 8); layout.Parent = page

    tabs[id] = { btn = btn, order = order }; pages[id] = page

    btn.MouseEnter:Connect(function()
        if activeTab ~= id then
            TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = 0.5, BackgroundColor3 = C.CardHover, TextColor3 = C.Text}):Play()
        end
    end)
    btn.MouseLeave:Connect(function()
        if activeTab ~= id then
            TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = 1, TextColor3 = C.TextDim}):Play()
        end
    end)
    btn.MouseButton1Click:Connect(function()
        if activeTab and tabs[activeTab] then
            local old = tabs[activeTab]
            TweenService:Create(old.btn, TweenInfo.new(0.25), {BackgroundTransparency = 1, BackgroundColor3 = C.Card, TextColor3 = C.TextDim}):Play()
            pages[activeTab].Visible = false
        end
        activeTab = id
        TweenService:Create(btn, TweenInfo.new(0.25), {BackgroundTransparency = 0.2, BackgroundColor3 = C.AccentDim, TextColor3 = C.Text}):Play()
        pages[id].Visible = true
        TweenService:Create(Indicator, TweenInfo.new(0.3, Enum.EasingStyle.Quint), {
            Position = UDim2.new(0, 0, 0, 12 + order * 46)
        }):Play()
    end)
    return page
end

local function createSection(parent, title)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -10, 0, 26); frame.BackgroundTransparency = 1
    frame.ZIndex = 8; frame.Parent = parent
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, 0, 1, 0); lbl.BackgroundTransparency = 1
    lbl.Text = title; lbl.TextColor3 = C.AccentGlow; lbl.TextSize = 12
    lbl.Font = Enum.Font.GothamBold; lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.ZIndex = 8; lbl.Parent = frame
    local underline = Instance.new("Frame")
    underline.Size = UDim2.new(0, 30, 0, 2); underline.Position = UDim2.new(0, 0, 1, -3)
    underline.BackgroundColor3 = C.Accent; underline.BorderSizePixel = 0
    underline.ZIndex = 8; underline.Parent = frame
    Instance.new("UICorner", underline).CornerRadius = UDim.new(1, 0)
    return frame
end

local function createButton(parent, text, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -10, 0, 38); btn.BackgroundColor3 = C.Card
    btn.BorderSizePixel = 0; btn.Text = text; btn.TextColor3 = C.Text
    btn.TextSize = 13; btn.Font = Enum.Font.GothamMedium
    btn.TextXAlignment = Enum.TextXAlignment.Left
    btn.AutoButtonColor = false; btn.ZIndex = 8; btn.Parent = parent
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 10)
    local pad = Instance.new("UIPadding"); pad.PaddingLeft = UDim.new(0, 14); pad.Parent = btn
    local stroke = Instance.new("UIStroke")
    stroke.Color = C.Border; stroke.Thickness = 1; stroke.Transparency = 0.5; stroke.Parent = btn
    btn.MouseEnter:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = C.CardHover}):Play()
        TweenService:Create(stroke, TweenInfo.new(0.2), {Color = C.Accent, Transparency = 0.2}):Play()
    end)
    btn.MouseLeave:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = C.Card}):Play()
        TweenService:Create(stroke, TweenInfo.new(0.2), {Color = C.Border, Transparency = 0.5}):Play()
    end)
    btn.MouseButton1Click:Connect(function() if callback then pcall(callback) end end)
    return btn
end

local function createToggle(parent, text, default, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -10, 0, 40); frame.BackgroundColor3 = C.Card
    frame.BorderSizePixel = 0; frame.ZIndex = 8; frame.Parent = parent
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)
    local stroke = Instance.new("UIStroke")
    stroke.Color = C.Border; stroke.Thickness = 1; stroke.Transparency = 0.5; stroke.Parent = frame

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -80, 1, 0); lbl.Position = UDim2.new(0, 14, 0, 0)
    lbl.BackgroundTransparency = 1; lbl.Text = text; lbl.TextColor3 = C.Text
    lbl.TextSize = 13; lbl.Font = Enum.Font.GothamMedium
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.ZIndex = 9; lbl.Parent = frame

    local track = Instance.new("Frame")
    track.Size = UDim2.new(0, 44, 0, 22); track.Position = UDim2.new(1, -58, 0.5, -11)
    track.BackgroundColor3 = default and C.Accent or C.CardHover
    track.BorderSizePixel = 0; track.ZIndex = 9; track.Parent = frame
    Instance.new("UICorner", track).CornerRadius = UDim.new(1, 0)

    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0, 16, 0, 16)
    knob.Position = default and UDim2.new(1, -19, 0.5, -8) or UDim2.new(0, 3, 0.5, -8)
    knob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    knob.BorderSizePixel = 0; knob.ZIndex = 10; knob.Parent = track
    Instance.new("UICorner", knob).CornerRadius = UDim.new(1, 0)

    local state = default
    local clickArea = Instance.new("TextButton")
    clickArea.Size = UDim2.new(1, 0, 1, 0); clickArea.BackgroundTransparency = 1
    clickArea.Text = ""; clickArea.ZIndex = 11; clickArea.Parent = frame

    clickArea.MouseButton1Click:Connect(function()
        state = not state
        TweenService:Create(track, TweenInfo.new(0.25, Enum.EasingStyle.Quint), {
            BackgroundColor3 = state and C.Accent or C.CardHover
        }):Play()
        TweenService:Create(knob, TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
            Position = state and UDim2.new(1, -19, 0.5, -8) or UDim2.new(0, 3, 0.5, -8)
        }):Play()
        if callback then pcall(callback, state) end
    end)
    return frame
end

local function createSlider(parent, text, minVal, maxVal, default, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -10, 0, 52); frame.BackgroundColor3 = C.Card
    frame.BorderSizePixel = 0; frame.ZIndex = 8; frame.Parent = parent
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -20, 0, 20); lbl.Position = UDim2.new(0, 14, 0, 6)
    lbl.BackgroundTransparency = 1; lbl.Text = text .. ": " .. tostring(default)
    lbl.TextColor3 = C.Text; lbl.TextSize = 12; lbl.Font = Enum.Font.GothamMedium
    lbl.TextXAlignment = Enum.TextXAlignment.Left; lbl.ZIndex = 9; lbl.Parent = frame

    local barBg = Instance.new("Frame")
    barBg.Size = UDim2.new(1, -28, 0, 8); barBg.Position = UDim2.new(0, 14, 1, -18)
    barBg.BackgroundColor3 = C.CardHover; barBg.BorderSizePixel = 0
    barBg.ZIndex = 9; barBg.Parent = frame
    Instance.new("UICorner", barBg).CornerRadius = UDim.new(1, 0)

    local fill = Instance.new("Frame")
    fill.Size = UDim2.new((default - minVal) / (maxVal - minVal), 0, 1, 0)
    fill.BackgroundColor3 = C.Accent; fill.BorderSizePixel = 0
    fill.ZIndex = 10; fill.Parent = barBg
    Instance.new("UICorner", fill).CornerRadius = UDim.new(1, 0)

    local dragging = false
    local function update(input)
        local rx = math.clamp((input.Position.X - barBg.AbsolutePosition.X) / barBg.AbsoluteSize.X, 0, 1)
        local v = math.floor(minVal + (maxVal - minVal) * rx)
        fill.Size = UDim2.new(rx, 0, 1, 0)
        lbl.Text = text .. ": " .. tostring(v)
        if callback then pcall(callback, v) end
    end
    barBg.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true; update(input)
        end
    end)
    barBg.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            update(input)
        end
    end)
    return frame
end

-- State
local bypassEnabled = false
local antiRagdollEnabled = false
local tpWalkEnabled = false
local tpWalkSpeedValue = 120
local flyEnabled = false
local flySpeedValue = 500
local autoHatchEnabled = false
local autoEquipBestEnabled = false
local autoClaimEnabled = false
local autoPenEnabled = true
local autoTreadmillEnabled = true
local autoRunEnabled = true
local autoBuyTrailsEnabled = true
local antiTrapEnabled = true
local antiAfkEnabled = false
local antiLagEnabled = false
local fullbrightEnabled = false
local activeTween = nil
local idledConnection = nil
local normalFlyGyro, normalFlyVel = nil, nil

local function fixCharacterState(char)
    if not char then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not hum then return end
    pcall(function() hum.PlatformStand = false end)
    pcall(function() hum.Sit = false end)
    pcall(function() hum:ChangeState(Enum.HumanoidStateType.Running) end)
    pcall(function() hum:ChangeState(Enum.HumanoidStateType.GettingUp) end)
    pcall(function() hum:ChangeState(Enum.HumanoidStateType.Landed) end)
    pcall(function() hum:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false) end)
    pcall(function() hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false) end)
    pcall(function() hum.WalkSpeed = 16 end)
    local root = char:FindFirstChild("HumanoidRootPart")
    if root and workspace.CurrentCamera then
        workspace.CurrentCamera.CameraSubject = hum
    end
end

local function executeBypass()
    local character = LocalPlayer.Character
    if not character then return end
    local humanoid = character:FindFirstChildOfClass("Humanoid")
    if not humanoid then return end
    local clone = humanoid:Clone()
    clone.Parent = character
    humanoid:Destroy()
    task.wait(0.1)
    local newHum = character:FindFirstChildOfClass("Humanoid")
    if newHum then
        newHum.PlatformStand = false
        newHum.Sit = false
        pcall(function() newHum:ChangeState(Enum.HumanoidStateType.Running) end)
        pcall(function() newHum:ChangeState(Enum.HumanoidStateType.GettingUp) end)
        newHum:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
        newHum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
    end
    local root = character:FindFirstChild("HumanoidRootPart")
    if root and workspace.CurrentCamera then
        workspace.CurrentCamera.CameraSubject = newHum or root
    end
end

LocalPlayer.CharacterAdded:Connect(function(char)
    task.wait(0.3)
    fixCharacterState(char)
    if bypassEnabled then
        task.wait(0.2)
        executeBypass()
    end
end)

-- Tabs
local mainPage = createTab("main", "Main", 0)
local settingPage = createTab("setting", "Setting", 1)
local upgradesPage = createTab("upgrades", "Upgrades", 2)
local aboutPage = createTab("about", "About", 3)

-- MAIN
createSection(mainPage, "BYPASS")
createToggle(mainPage, "Bypass Anti-Cheat", false, function(v)
    bypassEnabled = v
    if v then executeBypass() end
end)

createSection(mainPage, "MOVEMENT")
createToggle(mainPage, "TP Walk", false, function(v) tpWalkEnabled = v end)
createSlider(mainPage, "TP Walk Speed", 1, 1000, 120, function(v) tpWalkSpeedValue = v end)
createToggle(mainPage, "Anti-Ragdoll", false, function(v) antiRagdollEnabled = v end)
createToggle(mainPage, "Fly", false, function(v) flyEnabled = v end)
createSlider(mainPage, "Fly Speed", 1, 1000, 500, function(v) flySpeedValue = v end)

createSection(mainPage, "ACTIONS")
createButton(mainPage, "Respawn Character", function()
    local c = LocalPlayer.Character
    if c then local h = c:FindFirstChildOfClass("Humanoid"); if h then h.Health = 0 end end
end)
createButton(mainPage, "Go To Main Stand", function()
    local character = LocalPlayer.Character
    if not character then return end
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    local hum = character:FindFirstChildOfClass("Humanoid")
    if not rootPart then return end
    if activeTween then activeTween:Cancel() end
    local target = CFrame.new(544.577637, 92.0762939, -364.869049, -1, 0, 0, 0, 1, 0, 0, 0, -1)
    local dist = (rootPart.Position - target.Position).Magnitude
    activeTween = TweenService:Create(rootPart, TweenInfo.new(math.max(dist / 350, 0.1), Enum.EasingStyle.Linear), {CFrame = target})
    activeTween.Completed:Connect(function()
        if hum then pcall(function() hum:ChangeState(Enum.HumanoidStateType.Landed) hum.PlatformStand = false end) end
    end)
    activeTween:Play()
end)

-- =========================================================
-- SETTING
-- =========================================================
createSection(settingPage, "INTERFACE")
createToggle(settingPage, "Snow Effect", true, function(v) snowEnabled = v end)

-- SERVER HOP (dùng logic từ script tham khảo)
createSection(settingPage, "SERVER HOP")

local autoHopEnabled = false
local autoHopRunning = false

local function getLowPlayerServers()
    local url = "https://games.roblox.com/v1/games/" .. game.PlaceId .. "/servers/Public?sortOrder=Asc&limit=100"
    local ok, raw = pcall(function() return game:HttpGet(url) end)
    if not ok or not raw or raw == "" then return {} end

    local ok2, data = pcall(HttpService.JSONDecode, HttpService, raw)
    if not ok2 or not data or not data.data then return {} end

    local currentId = tostring(game.JobId)
    local result = {}
    for _, s in ipairs(data.data) do
        if s.id and tostring(s.id) ~= currentId
        and typeof(s.playing) == "number"
        and typeof(s.maxPlayers) == "number"
        and s.playing >= 1 and s.playing <= 2
        and s.playing < s.maxPlayers then
            table.insert(result, {
                id = tostring(s.id),
                playing = s.playing,
                maxPlayers = s.maxPlayers,
            })
        end
    end

    table.sort(result, function(a, b)
        if a.playing ~= b.playing then return a.playing < b.playing end
        return a.maxPlayers > b.maxPlayers
    end)

    return result
end

createToggle(settingPage, "Auto Hop", false, function(v)
    autoHopEnabled = v
    if v and not autoHopRunning then
        autoHopRunning = true
        print("╔════════════════════════════════════════╗")
        print("║  AUTO HOP: ON                          ║")
        print("╚════════════════════════════════════════╝")

        task.spawn(function()
            while autoHopEnabled do
                local currentCount = #Players:GetPlayers()

                if currentCount <= 2 then
                    print(string.format("[AUTO HOP] Server %d nguoi, dung", currentCount))
                    task.wait(3)
                else
                    print("[AUTO HOP] Dang quet server 1-2 nguoi...")
                    local servers = getLowPlayerServers()

                    if #servers > 0 then
                        local best = servers[1]
                        print(string.format("[AUTO HOP] Join %s (%d/%d)",
                            best.id:sub(1, 8), best.playing, best.maxPlayers))

                        local ok = pcall(function()
                            TeleportService:TeleportToPlaceInstance(game.PlaceId, best.id, LocalPlayer)
                        end)
                        if ok then
                            print("[AUTO HOP] Teleport thanh cong!")
                            return
                        else
                            task.wait(0.5)
                        end
                    else
                        print("[AUTO HOP] Khong co server 1-2 nguoi, quet lai...")
                        task.wait(1)
                    end
                end
            end
            autoHopRunning = false
        end)
    else
        autoHopRunning = false
        print("[AUTO HOP] OFF")
    end
end)

-- PROTECTION
createSection(settingPage, "PROTECTION")
createToggle(settingPage, "Anti-Trap", true, function(v) antiTrapEnabled = v end)
createToggle(settingPage, "Anti-AFK", false, function(v)
    antiAfkEnabled = v
    if antiAfkEnabled then
        if getconnections then
            for _, conn in pairs(getconnections(LocalPlayer.Idled)) do conn:Disable() end
        else
            if not idledConnection then
                idledConnection = LocalPlayer.Idled:Connect(function()
                    pcall(function()
                        local vu = game:GetService("VirtualUser")
                        vu:CaptureController(); vu:ClickButton2(Vector2.new())
                    end)
                end)
            end
        end
    else
        if getconnections then
            for _, conn in pairs(getconnections(LocalPlayer.Idled)) do conn:Enable() end
        end
        if idledConnection then idledConnection:Disconnect() idledConnection = nil end
    end
end)
createToggle(settingPage, "Anti-Lag", false, function(v)
    antiLagEnabled = v
    pcall(function()
        if antiLagEnabled then
            Lighting.GlobalShadows = false; Lighting.FogEnd = 999999
            for _, obj in ipairs(Workspace:GetDescendants()) do
                if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Fire") or obj:IsA("Smoke") or obj:IsA("Sparkles") then
                    obj.Enabled = false
                end
            end
        else
            Lighting.GlobalShadows = true; Lighting.FogEnd = 100000
        end
    end)
end)

createSection(settingPage, "VISUALS")
createToggle(settingPage, "Fullbright", false, function(v)
    fullbrightEnabled = v
    Lighting.Brightness = fullbrightEnabled and 3 or 1
    Lighting.GlobalShadows = not fullbrightEnabled
end)

createSection(settingPage, "DANGER")
createButton(settingPage, "Unload Script", function()
    pcall(function()
        if ScreenGui then ScreenGui:Destroy() end
        if ToggleGui then ToggleGui:Destroy() end
    end)
end)

-- UPGRADES
createSection(upgradesPage, "AUTO UPGRADES")
createToggle(upgradesPage, "Auto Upgrade Pen", true, function(v) autoPenEnabled = v end)
createToggle(upgradesPage, "Auto Upgrade Treadmill", true, function(v) autoTreadmillEnabled = v end)
createToggle(upgradesPage, "Auto Buy Trails", true, function(v) autoBuyTrailsEnabled = v end)
createToggle(upgradesPage, "Auto Run Treadmill", true, function(v) autoRunEnabled = v end)

createSection(upgradesPage, "AUTO REWARDS")
createToggle(upgradesPage, "Auto Hatch", false, function(v) autoHatchEnabled = v end)
createToggle(upgradesPage, "Auto Equip Best", false, function(v) autoEquipBestEnabled = v end)
createToggle(upgradesPage, "Auto Claim", false, function(v) autoClaimEnabled = v end)
createButton(upgradesPage, "Sell Inventory Now", function()
    task.spawn(function()
        pcall(function()
            local net = ReplicatedStorage:FindFirstChild("Packages") and ReplicatedStorage.Packages:FindFirstChild("Networking")
            if net then
                if net:FindFirstChild("RE/PetSatchel/SellEveryPet") then net["RE/PetSatchel/SellEveryPet"]:FireServer() end
                if net:FindFirstChild("RF/Haul/OfferFullSatchelSale") then net["RF/Haul/OfferFullSatchelSale"]:InvokeServer() end
            end
        end)
    end)
end)

-- ABOUT
createSection(aboutPage, "CREDITS")
local oLbl = Instance.new("TextLabel")
oLbl.Size = UDim2.new(1, -10, 0, 32); oLbl.BackgroundColor3 = C.Card; oLbl.BorderSizePixel = 0
oLbl.Text = "Owner:  " .. OWNER; oLbl.TextColor3 = C.Text; oLbl.TextSize = 13
oLbl.Font = Enum.Font.GothamMedium; oLbl.TextXAlignment = Enum.TextXAlignment.Left
oLbl.ZIndex = 8; oLbl.Parent = aboutPage
Instance.new("UICorner", oLbl).CornerRadius = UDim.new(0, 8)
local p1 = Instance.new("UIPadding"); p1.PaddingLeft = UDim.new(0, 14); p1.Parent = oLbl

local dLbl = Instance.new("TextLabel")
dLbl.Size = UDim2.new(1, -10, 0, 32); dLbl.BackgroundColor3 = C.Card; dLbl.BorderSizePixel = 0
dLbl.Text = "Dev:  " .. DEV; dLbl.TextColor3 = C.Text; dLbl.TextSize = 13
dLbl.Font = Enum.Font.GothamMedium; dLbl.TextXAlignment = Enum.TextXAlignment.Left
dLbl.ZIndex = 8; dLbl.Parent = aboutPage
Instance.new("UICorner", dLbl).CornerRadius = UDim.new(0, 8)
local p2 = Instance.new("UIPadding"); p2.PaddingLeft = UDim.new(0, 14); p2.Parent = dLbl

createSection(aboutPage, "COMMUNITY")
local dBtn = Instance.new("TextButton")
dBtn.Size = UDim2.new(1, -10, 0, 46); dBtn.BackgroundColor3 = C.Discord
dBtn.BorderSizePixel = 0; dBtn.Text = "Join Discord Server"
dBtn.TextColor3 = Color3.fromRGB(255, 255, 255); dBtn.TextSize = 13
dBtn.Font = Enum.Font.GothamBold; dBtn.AutoButtonColor = false
dBtn.ZIndex = 8; dBtn.Parent = aboutPage
Instance.new("UICorner", dBtn).CornerRadius = UDim.new(0, 10)
dBtn.MouseButton1Click:Connect(function()
    local ok = pcall(function()
        if setclipboard then setclipboard(DISCORD_LINK)
        elseif toclipboard then toclipboard(DISCORD_LINK) end
    end)
    if ok then
        local n = Instance.new("TextLabel")
        n.Size = UDim2.new(0, 320, 0, 40); n.Position = UDim2.new(0.5, -160, 0, 20)
        n.BackgroundColor3 = C.Discord; n.BorderSizePixel = 0
        n.Text = "Da copy link Discord!"; n.TextColor3 = Color3.fromRGB(255, 255, 255)
        n.TextSize = 13; n.Font = Enum.Font.GothamBold; n.ZIndex = 200; n.Parent = ScreenGui
        Instance.new("UICorner", n).CornerRadius = UDim.new(0, 10)
        task.delay(2.5, function()
            TweenService:Create(n, TweenInfo.new(0.4), {BackgroundTransparency = 1, TextTransparency = 1}):Play()
            task.wait(0.4); n:Destroy()
        end)
    end
end)

local lLbl = Instance.new("TextLabel")
lLbl.Size = UDim2.new(1, -10, 0, 30); lLbl.BackgroundColor3 = C.BgLight; lLbl.BorderSizePixel = 0
lLbl.Text = "discord.gg/HMfsMcUWH2"; lLbl.TextColor3 = C.AccentGlow
lLbl.TextSize = 12; lLbl.Font = Enum.Font.Code; lLbl.ZIndex = 8; lLbl.Parent = aboutPage
Instance.new("UICorner", lLbl).CornerRadius = UDim.new(0, 8)

-- Active tab
task.wait(0.1)
activeTab = "main"
tabs.main.btn.BackgroundTransparency = 0.2
tabs.main.btn.BackgroundColor3 = C.AccentDim
tabs.main.btn.TextColor3 = C.Text
pages.main.Visible = true
Indicator.Position = UDim2.new(0, 0, 0, 12)

-- LOOPS
task.spawn(function()
    while task.wait(0.2) do
        if antiRagdollEnabled then
            local char = LocalPlayer.Character
            if char then
                local hum = char:FindFirstChildOfClass("Humanoid")
                if hum then
                    hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
                    hum:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
                    local st = hum:GetState()
                    if st == Enum.HumanoidStateType.Physics or st == Enum.HumanoidStateType.FallingDown or st == Enum.HumanoidStateType.Ragdoll then
                        pcall(function() hum:ChangeState(Enum.HumanoidStateType.GettingUp) end)
                    end
                    if not flyEnabled then hum.PlatformStand = false end
                end
                for _, v in ipairs(char:GetDescendants()) do
                    if v:IsA("BodyVelocity") or v:IsA("BodyForce") or v:IsA("BodyPosition") or v:IsA("LinearVelocity") or v:IsA("VectorForce") then
                        if v.Name ~= "CustomFlyVel" and v.Name ~= "CustomFlyGyro" then pcall(function() v:Destroy() end) end
                    elseif v:IsA("Motor6D") and not v.Enabled then v.Enabled = true end
                end
            end
        end
    end
end)

task.spawn(function()
    local function checkAndDestroy(obj)
        pcall(function()
            if obj:IsA("Model") or obj:IsA("BasePart") then
                local n = string.lower(obj.Name)
                if string.find(n, "trap") or string.find(n, "spike") or string.find(n, "snare") then
                    obj:Destroy()
                end
            end
        end)
    end
    for _, obj in ipairs(Workspace:GetDescendants()) do checkAndDestroy(obj) end
    Workspace.DescendantAdded:Connect(function(obj) if antiTrapEnabled then checkAndDestroy(obj) end end)
end)

RunService.RenderStepped:Connect(function(dt)
    local char = LocalPlayer.Character
    if not char then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    local rootPart = char:FindFirstChild("HumanoidRootPart")
    if not hum or not rootPart then return end

    if tpWalkEnabled then
        if hum.MoveDirection.Magnitude > 0 then
            local vel = hum.MoveDirection * math.clamp(tpWalkSpeedValue, 1, 1000)
            rootPart.AssemblyLinearVelocity = Vector3.new(vel.X, rootPart.AssemblyLinearVelocity.Y, vel.Z)
        else
            rootPart.AssemblyLinearVelocity = Vector3.new(0, rootPart.AssemblyLinearVelocity.Y, 0)
        end
    end

    if flyEnabled then
        if not normalFlyGyro or not normalFlyGyro.Parent then
            normalFlyGyro = Instance.new("BodyGyro")
            normalFlyGyro.Name = "CustomFlyGyro"
            normalFlyGyro.P = 9e4
            normalFlyGyro.MaxTorque = Vector3.new(9e4, 9e4, 9e4)
            normalFlyGyro.Parent = rootPart
        end
        if not normalFlyVel or not normalFlyVel.Parent then
            normalFlyVel = Instance.new("BodyVelocity")
            normalFlyVel.Name = "CustomFlyVel"
            normalFlyVel.Velocity = Vector3.zero
            normalFlyVel.MaxForce = Vector3.new(9e4, 9e4, 9e4)
            normalFlyVel.Parent = rootPart
        end
        local cam = Workspace.CurrentCamera
        local moveDir = Vector3.zero
        if not UserInputService:GetFocusedTextBox() then
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - cam.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + cam.CFrame.RightVector end
        end
        normalFlyGyro.CFrame = cam.CFrame
        normalFlyVel.Velocity = moveDir * math.clamp(flySpeedValue, 1, 1000)
        hum.PlatformStand = true
    else
        if normalFlyGyro then normalFlyGyro:Destroy() normalFlyGyro = nil end
        if normalFlyVel then normalFlyVel:Destroy() normalFlyVel = nil end
        if hum and hum.PlatformStand and not antiRagdollEnabled then
            hum.PlatformStand = false
            pcall(function() hum:ChangeState(Enum.HumanoidStateType.Landed) end)
        end
    end
end)

task.spawn(function()
    local lastHatch, lastEquip, lastClaim = 0, 0, 0
    while task.wait(1) do
        local now = os.clock()
        local net = ReplicatedStorage:FindFirstChild("Packages") and ReplicatedStorage.Packages:FindFirstChild("Networking")
        if autoHatchEnabled and (now - lastHatch) >= 2 then
            lastHatch = now
            pcall(function()
                if getconnections then
                    for _, gui in ipairs(LocalPlayer.PlayerGui:GetDescendants()) do
                        if (gui:IsA("TextButton") or gui:IsA("ImageButton")) and gui.Visible then
                            local t = gui:IsA("TextButton") and gui.Text or ""
                            if t == "" then local tl = gui:FindFirstChildOfClass("TextLabel"); if tl then t = tl.Text end end
                            if t == "Open" or t == "Grow All" then
                                for _, c in pairs(getconnections(gui.MouseButton1Click)) do c:Fire() end
                            end
                        end
                    end
                end
            end)
        end
        if autoEquipBestEnabled and (now - lastEquip) >= 3 then
            lastEquip = now
            pcall(function()
                if net and net:FindFirstChild("RF/Haul/WearBest") then net["RF/Haul/WearBest"]:InvokeServer() end
            end)
        end
        if autoClaimEnabled and (now - lastClaim) >= 5 then
            lastClaim = now
            pcall(function()
                if net then
                    if net:FindFirstChild("RF/AwayEarnings/AskCollect") then pcall(function() net["RF/AwayEarnings/AskCollect"]:InvokeServer() end) end
                    if net:FindFirstChild("RF/Codex/AskRedeemAll") then pcall(function() net["RF/Codex/AskRedeemAll"]:InvokeServer() end) end
                end
            end)
        end
    end
end)

task.spawn(function()
    local lastPen, lastTread, lastTrail = 0, 0, 0
    while true do
        if autoPenEnabled or autoTreadmillEnabled or autoBuyTrailsEnabled then
            local ok, data = pcall(function() return Save and Save.Get and Save.Get() end)
            if ok and data then
                local now = os.clock()
                if autoPenEnabled and (now - lastPen > 1.5) then
                    local lvl = data.BaseUpgradeLevel + 1
                    local cfg = Bases and Bases.BASES and Bases.BASES[lvl]
                    if cfg and data.Money >= cfg.Cost then
                        pcall(function()
                            if PlotsNet and PlotsNet.REQUEST_BASE_UPGRADE then Network.Fire(PlotsNet.REQUEST_BASE_UPGRADE)
                            elseif Network and Network.Fire then Network.Fire("REQUEST_BASE_UPGRADE") end
                        end)
                    end
                    lastPen = now
                end
                if autoTreadmillEnabled and (now - lastTread > 1.5) then
                    local lvl = data.TreadmillUpgradeLevel + 1
                    local cfg = Treadmills and Treadmills.GetByUpgradeLevel and Treadmills.GetByUpgradeLevel(lvl)
                    if cfg and data.Money >= cfg.Price then
                        pcall(function()
                            if TreadmillsNet and TreadmillsNet.REQUEST_UPGRADE then Network.Invoke(TreadmillsNet.REQUEST_UPGRADE, cfg._id)
                            elseif Network and Network.Invoke then Network.Invoke("REQUEST_UPGRADE", cfg._id) end
                        end)
                    end
                    lastTread = now
                end
                if autoBuyTrailsEnabled and (now - lastTrail > 3) then
                    if Trails and Trails.Directory then
                        local affordable = {}
                        for name, cfg in pairs(Trails.Directory) do
                            if cfg.DisplayInShop and not data.TrailInventory[name] and data.Money >= cfg.Price then
                                table.insert(affordable, {name = name, price = cfg.Price})
                            end
                        end
                        table.sort(affordable, function(a, b) return a.price < b.price end)
                        if #affordable > 0 then
                            local t = affordable[#affordable]
                            pcall(function()
                                if TrailsNet and TrailsNet.REQUEST_PURCHASE then Network.Invoke(TrailsNet.REQUEST_PURCHASE, t.name)
                                elseif Network and Network.Invoke then Network.Invoke("REQUEST_PURCHASE", t.name) end
                            end)
                        end
                    end
                    lastTrail = now
                end
            end
        end
        task.wait(1.5)
    end
end)

task.spawn(function()
    while true do
        if autoRunEnabled then
            pcall(function()
                if TreadmillsNet and TreadmillsNet.REQUEST_SET_SLOW_TOGGLE_ENABLED then
                    Network.Invoke(TreadmillsNet.REQUEST_SET_SLOW_TOGGLE_ENABLED, false)
                elseif Network and Network.Invoke then
                    Network.Invoke("REQUEST_SET_SLOW_TOGGLE_ENABLED", false)
                end
            end)
        end
        task.wait(10)
    end
end)

-- Floating Button
local FB = Instance.new("TextButton")
FB.Size = UDim2.new(0, 60, 0, 60)
FB.Position = UDim2.new(0, 24, 0.2, -30)
FB.BackgroundColor3 = C.Accent
FB.BorderSizePixel = 0
FB.Text = "OH"
FB.TextColor3 = Color3.fromRGB(255, 255, 255)
FB.TextSize = 20
FB.Font = Enum.Font.GothamBold
FB.AutoButtonColor = false
FB.Active = true
FB.Draggable = false
FB.Parent = ToggleGui
Instance.new("UICorner", FB).CornerRadius = UDim.new(1, 0)

local fS = Instance.new("UIStroke")
fS.Color = C.AccentGlow; fS.Thickness = 2; fS.Transparency = 0.3; fS.Parent = FB

task.spawn(function()
    while FB.Parent do
        TweenService:Create(fS, TweenInfo.new(1.5, Enum.EasingStyle.Sine), {Thickness = 4, Transparency = 0.7}):Play()
        task.wait(1.5)
        if not FB.Parent then break end
        TweenService:Create(fS, TweenInfo.new(1.5, Enum.EasingStyle.Sine), {Thickness = 2, Transparency = 0.3}):Play()
        task.wait(1.5)
    end
end)

local dragging = false
local dragStart, startPos, dragMoved = nil, nil, false

FB.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true; dragMoved = false
        dragStart = input.Position; startPos = FB.Position
        TweenService:Create(FB, TweenInfo.new(0.1), {Size = UDim2.new(0, 56, 0, 56)}):Play()
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        if math.abs(delta.X) > 3 or math.abs(delta.Y) > 3 then dragMoved = true end
        FB.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

FB.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
        TweenService:Create(FB, TweenInfo.new(0.1), {Size = UDim2.new(0, 60, 0, 60)}):Play()
        if not dragMoved then ScreenGui.Enabled = not ScreenGui.Enabled end
    end
end)

UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.RightControl then
        ScreenGui.Enabled = not ScreenGui.Enabled
    end
end)

print("╔══════════════════════════════════════════╗")
print("║  OREO HUB loaded                          ║")
print("║  Owner: " .. OWNER)
print("║  Dev: " .. DEV)
print("╚══════════════════════════════════════════╝")

-- ==========================================================================
-- OREO HUB FULL + BLYXO FEATURE PACK
-- Added: Auto Steal, Egg/Plot ESP, Anti Treadmill, Boss Event
-- Isolated in its own function scope to avoid Luau top-level register pressure.
-- ==========================================================================
task.spawn(function()
-- ================= BLYXO 4.1.0 RUNTIME / MODULES =================
local BLYXO_VERSION = "4.1.0"
local BLYXO_BUILD = "8dc2057f"

--[[ ==== boot/00_runtime.lua ========================================= ]]
-- =============================================================================
-- RUNTIME: module registry + single-instance guard
-- =============================================================================
--
-- Everything in this hub lives inside a module. A module is a NAME and a
-- FACTORY FUNCTION, and the factory does not run until something asks for the
-- module. Two things come out of that:
--
--   1. Every module body is its own function scope, so its locals are its own.
--      The old single-chunk build kept hitting Luau's ceiling -
--          Out of local registers ... exceeded 200
--      - and every new feature had to be contorted to avoid declaring a local.
--      That ceiling is per-scope, so it simply does not apply here.
--
--   2. A module that throws is caught by name. The failure names the module
--      instead of surfacing as a bare line number in an 18,000-line file.
--
-- Usage:
--     BX.module("core.util", function(BX)
--         local M = {}
--         function M.clamp(v, lo, hi) return math.max(lo, math.min(hi, v)) end
--         return M
--     end)
--
--     local util = BX.require("core.util")

local env = (type(getgenv) == "function" and getgenv()) or _G

-- ONE INSTANCE AT A TIME.
--
-- Re-running the script (a second paste, a key-system loader firing twice, an
-- auto-exec on respawn) used to build a second copy of everything: another
-- window, another steal loop fighting the first for the character, and another
-- set of background loops. Doubled remote traffic and two movers arguing over
-- the same HumanoidRootPart - a good share of the old "it gets laggy after a
-- while" reports.
--
-- Each generation gets a number. Every loop checks its own generation and
-- returns the moment it is no longer current, so the previous copy unwinds
-- itself without needing to cooperate.
env.BlyxoGeneration = (env.BlyxoGeneration or 0) + 1

local BX = {
    generation  = env.BlyxoGeneration,
    version     = BLYXO_VERSION,
    build       = BLYXO_BUILD,
    _factories  = {},
    _loaded     = {},
    _loading    = {},
    _conns      = {},
}
env.BX = BX

-- True while this copy is still the newest one. Every long-running loop must
-- test this and return when it goes false.
function BX.alive()
    return env.BlyxoGeneration == BX.generation
end

function BX.module(name, factory)
    if BX._factories[name] then
        error(("duplicate module %q"):format(name), 2)
    end
    BX._factories[name] = factory
end

function BX.require(name)
    local cached = BX._loaded[name]
    if cached ~= nil then return cached end

    if BX._loading[name] then
        error(("circular dependency: %s"):format(name), 2)
    end
    local factory = BX._factories[name]
    if not factory then
        error(("no such module: %s"):format(name), 2)
    end

    BX._loading[name] = true
    local ok, result = pcall(factory, BX)
    BX._loading[name] = nil

    if not ok then
        -- Raised, not swallowed. A module that cannot build is a broken build,
        -- and the caller decides whether that is fatal. The name is in the
        -- message, which is the entire point of loading modules by name.
        error(("module %q failed to load: %s"):format(name, tostring(result)), 2)
    end
    if result == nil then
        error(("module %q returned nil (forgot to return M?)"):format(name), 2)
    end

    BX._loaded[name] = result
    return result
end

-- Connection bookkeeping. The old build leaked every Heartbeat/RenderStepped
-- handler it ever opened: re-executing left the previous copy's handlers
-- running every frame, holding all of its tables alive. Memory climbed with
-- each re-run. Anything connected through here is disconnected on teardown.
function BX.connect(signal, fn)
    local c = signal:Connect(fn)
    BX._conns[#BX._conns + 1] = c
    return c
end

-- RUN THIS SOMEWHERE ELSE, THEN COME BACK.
--
-- Roblox capability sandbox NARROWS a thread when it calls into game script
-- code, and the narrowing PERSISTS after the call returns. Once narrowed, the
-- thread can no longer touch Instances:
--
--     The current thread cannot access Instance (lacking capability Plugin)
--
-- So reading egg data (which requires and calls the game own modules) and then
-- writing to a UI element on the same thread throws - the read poisons the
-- thread for the write. V3.1 hit this exactly here, on dropdown Refresh.
--
-- coroutine.resume does NOT confine the narrowing; task.spawn does, because the
-- scheduler runs the closure as a genuinely separate thread we never resume
-- ourselves. So the game-code call happens over there and the caller thread
-- stays clean for the Instance write.
--
-- This YIELDS, so it must not be called from anywhere that cannot wait.
function BX.offthread(fn, timeout)
    local done, result = false, nil
    task.spawn(function()
        local ok, r = pcall(fn)
        if ok then result = r end
        done = true
    end)

    -- COUNT REAL SECONDS, NOT TICKS. V3.1 added 0.03 per iteration and compared
    -- that to the timeout as though task.wait(0.03) always took 0.03. It takes
    -- at least one frame, so on a phone at 15fps a "5 second" timeout ran 166
    -- iterations at 0.067s - eleven seconds of the main thread doing nothing.
    -- The slower the client, the longer it hangs, which is the wrong way round.
    local startedAt = os.clock()
    timeout = timeout or 5
    while not done and (os.clock() - startedAt) < timeout do
        task.wait(0.03)
    end
    return result, done
end

function BX.teardown()
    -- THE TAIL OF THE LOG, BEFORE ANYTHING ELSE IS DROPPED.
    --
    -- The trace file is written by a background flusher now rather than by every
    -- log line, so the last second of a retired copy is still only in memory.
    -- Writing it here is what keeps "what was it doing when I re-executed" in
    -- the file - and it has to happen before BX._loaded is cleared, because that
    -- is what the logger lives in.
    pcall(function()
        local lg = BX._loaded["boot.log"]
        if lg and lg.flushNow then lg.flushNow() end
    end)

    -- Scopes first: they own most of what the hub creates, and a scope's
    -- destroy cancels threads that would otherwise still be mid-loop while the
    -- rest of teardown runs.
    if BX.destroyAllScopes then pcall(BX.destroyAllScopes) end
    for _, c in ipairs(BX._conns) do
        pcall(function() c:Disconnect() end)
    end
    BX._conns = {}
    -- Drop the module instances too. Without this a retired copy of the hub
    -- keeps every table, cache and captured Instance its modules ever built,
    -- which is the "memory climbs with each re-execute" case.
    BX._loaded = {}
end

-- Retire whatever the previous copy left connected.
if type(env.BlyxoTeardown) == "function" then
    pcall(env.BlyxoTeardown)
end
env.BlyxoTeardown = BX.teardown

--[[ ==== boot/01_log.lua ============================================= ]]
-- =============================================================================
-- LOG: levels, module tags, and the replacement for silent pcall
-- =============================================================================
--
-- The V3.1 build had 478 pcall sites and 329 of them discarded the error
-- completely - bare `pcall(f)` with no `ok, err`. Those are where bugs went to
-- hide: a call would fail every frame and nothing anywhere said so.
--
-- BX.try is the fix. Same protection, but the failure is recorded with the
-- label of whatever was being attempted:
--
--     BX.try("carry.pickup", function() ... end)
--
-- and it is recorded ONCE per label per session, not once per frame - a
-- failure inside a Heartbeat handler would otherwise bury the log in seconds.
-- The repeat count is kept and reported at teardown.

BX.module("boot.log", function(BX)
    local M = {}

    local TRACE_FILE  = "BlyxoHub_trace.txt"
    local FLUSH_GAP   = 1.0   -- seconds between disk writes
    local RING        = 500   -- lines kept in memory

    local canWrite  = (type(writefile) == "function")
    local debugOn   = function()
        local env = (type(getgenv) == "function" and getgenv()) or _G
        return env.BlyxoDebug == true
    end

    local ring, ringN, ringHead = {}, 0, 0
    local flushAt     = 0
    local seen, seenN = {}, 0   -- label -> repeat count, for BX.try
    local SEEN_MAX    = 400     -- distinct labels before we stop adding new ones

    M.LEVELS = { TRACE = 1, INFO = 2, WARN = 3, ERROR = 4 }
    M.level  = M.LEVELS.INFO

    local function stamp()
        return ("%7.2f"):format(os.clock())
    end

    -- THE OLD BUILD'S WORST PERFORMANCE BUG LIVED HERE.
    --
    -- It concatenated the whole buffer and writefile'd it on EVERY trace call.
    -- A busy second meant dozens of full-log disk writes, each rebuilding a
    -- string of several kilobytes, on the same thread as the steal loop.
    --
    -- Writes are timed now. At worst the last second is missing after a kick,
    -- and session markers and errors flush immediately, so the head of a run
    -- and every failure are always on disk.
    --
    -- AND THE TIMED WRITE NO LONGER HAPPENS ON THE CALLER'S THREAD.
    --
    -- Measured on the live client: one writefile of the 7.7KB trace costs
    -- 1.5-2.6ms. Because emit() called flush() directly, whichever unlucky log
    -- line first crossed the one-second gap PAID for it - and on a UI callback
    -- that is 1.5-2.6ms added to a click for nothing the user asked for. Worse,
    -- every WARN forced a write with no gap at all, so a handler warning in a
    -- burst rewrote the whole buffer once per warning.
    --
    -- An INFO/TRACE/WARN line now only marks the buffer dirty and the flusher
    -- thread below does the write on its own time. ERROR and session markers
    -- still write through immediately, because those are precisely the lines
    -- that have to survive a crash one instant later.
    local dirty = false

    local function writeNow()
        if not canWrite then return end
        flushAt = os.clock()
        dirty = false
        -- Oldest first. The buffer is circular, so the read starts just past
        -- the write head once it has wrapped.
        local out, n = {}, 0
        local start = (ringN < RING) and 1 or (ringHead % RING) + 1
        for i = 0, ringN - 1 do
            n = n + 1
            out[n] = ring[((start - 1 + i) % RING) + 1]
        end
        pcall(writefile, TRACE_FILE, table.concat(out, "\n", 1, n))
    end

    -- force: write through now (ERROR, session markers).
    -- otherwise: mark it dirty and let the flusher thread deal with it.
    local function flush(force)
        if not canWrite then return end
        if force then return writeNow() end
        dirty = true
    end

    -- ONE THREAD FOR THE SESSION, AND IT YIELDS UNCONDITIONALLY.
    --
    -- Deliberately not a scope: the logger is infrastructure that outlives every
    -- feature and has to still be writing while a teardown is being logged.
    -- BX.alive() retires it when this copy of the hub is replaced, and the last
    -- buffer goes out on the way past so a re-execute never loses the tail.
    if canWrite then
        task.spawn(function()
            while BX.alive() do
                task.wait(FLUSH_GAP)
                if dirty then pcall(writeNow) end
            end
            if dirty then pcall(writeNow) end
        end)
    end

    -- For teardown, and for anyone who wants the file current right now.
    function M.flushNow() pcall(writeNow) end

    -- Written once, not rebuilt per call. The old version indexed a fresh
    -- {"TRACE","INFO",...} table literal on EVERY log line - a throwaway
    -- four-element table for each one.
    local TAGS = { "TRACE", "INFO", "WARN", "ERROR" }

    local function emit(level, mod, msg)
        if level < M.level then return end
        local line = ("[%s] %-5s %-16s %s"):format(stamp(), TAGS[level], mod, msg)

        -- CIRCULAR, NOT A SHIFTING ARRAY.
        --
        -- This was table.remove(ring, 1) once the buffer was full, which shifts
        -- all 500 elements down by one FOR EVERY LINE LOGGED. Over a long
        -- session that is the logger quietly becoming one of the more expensive
        -- things the hub does. A write index wrapping round costs nothing.
        ringHead = (ringHead % RING) + 1
        ring[ringHead] = line
        if ringN < RING then ringN = ringN + 1 end

        if debugOn() or level >= M.LEVELS.WARN then
            print("[BLYXO] " .. line)
        end
        -- ERROR ONLY. A WARN used to force the whole buffer to disk on the
        -- calling thread, which is 1.5-2.6ms charged to whatever was running.
        flush(level >= M.LEVELS.ERROR)
    end

    -- A logger bound to one module name, so call sites stay short and every
    -- line says where it came from without the caller repeating itself.
    function M.for_module(name)
        return {
            trace = function(m, ...) emit(1, name, select("#", ...) > 0 and m:format(...) or m) end,
            info  = function(m, ...) emit(2, name, select("#", ...) > 0 and m:format(...) or m) end,
            warn  = function(m, ...) emit(3, name, select("#", ...) > 0 and m:format(...) or m) end,
            error = function(m, ...) emit(4, name, select("#", ...) > 0 and m:format(...) or m) end,
        }
    end

    function M.session(msg)
        emit(2, "session", "=== " .. msg .. " ===")
        flush(true)
    end

    function M.repeats()
        local out = {}
        for label, n in pairs(seen) do
            if n > 1 then out[#out + 1] = ("%s x%d"):format(label, n) end
        end
        table.sort(out)
        return out
    end

    -- BX.try - the replacement for bare pcall.
    --
    -- Protects the call the same way, but a failure is LOGGED against `label`
    -- rather than vanishing. Logged once; later failures of the same label
    -- only bump a counter, so a handler failing every frame costs one line.
    --
    -- Returns ok, result - so it substitutes directly for pcall at a call site
    -- that already checked the first return.
    function BX.try(label, fn, ...)
        local ok, result = pcall(fn, ...)
        if not ok then
            -- A label built from a changing value (an egg uid, a player name)
            -- would otherwise grow this table without limit for the whole
            -- session. Past the cap, new labels collapse into one bucket.
            if seen[label] == nil then
                if seenN >= SEEN_MAX then
                    label = "(other)"
                else
                    seenN = seenN + 1
                end
            end
            local n = (seen[label] or 0) + 1
            seen[label] = n
            if n == 1 then
                emit(4, "try", ("%s: %s"):format(label, tostring(result)))
            elseif n == 10 or n == 100 or n == 1000 then
                emit(3, "try", ("%s: still failing (x%d)"):format(label, n))
            end
        end
        return ok, result
    end

    -- Wrap a signal handler once; every invocation is protected and labelled.
    function BX.guard(label, fn)
        return function(...)
            return select(2, BX.try(label, fn, ...))
        end
    end

    M._emit = emit
    M._seen = seen
    return M
end)

--[[ ==== boot/02_scope.lua =========================================== ]]
-- =============================================================================
-- SCOPE: per-feature lifetime. Everything a feature creates, cleaned up.
-- =============================================================================
--
-- This is the answer to every leak class at once. A feature never connects,
-- spawns or creates directly; it does all of it through a scope, and when the
-- feature is switched off, the player respawns, or the script is re-executed,
-- ONE call takes the whole thing down.
--
--     local sc = BX.scope("features.autosteal")
--
--     sc:connect(RunService.Heartbeat, fn)   -- disconnected on destroy
--     sc:own(Instance.new("Highlight"))      -- destroyed on destroy
--     sc:spawn("label", fn)                  -- thread cancelled on destroy
--     sc:loop("label", 0.5, fn)              -- loop exits on destroy
--     sc:delay("label", 2, fn)               -- never fires after destroy
--     sc:tween(obj, 0.3, {...})              -- cancelled on destroy
--
--     sc:destroy()                           -- all of the above, gone
--
-- WHY THIS EXISTS. V3.1 had 59 :Connect calls and 26 :Disconnect calls. The
-- 33-connection gap is not a rounding error - those handlers ran for the rest
-- of the session, and every closure they held kept its tables alive. It had 17
-- `while true` loops, most with no exit condition beyond a generation check
-- that had to be remembered and written by hand at each site. Forgetting one
-- was invisible until the frame rate told you.
--
-- The rule for every module from here on: if it connects, spawns, loops,
-- delays, tweens or creates an Instance, it does so through its scope. A
-- module that does not touch a scope has nothing to leak.

BX._scopes = {}

function BX.scope(name)
    -- Re-entering a scope name always retires the previous one first. This is
    -- what makes a toggle safe to flip repeatedly and a feature safe to rebuild
    -- on respawn: there can never be two live copies of the same scope.
    local existing = BX._scopes[name]
    if existing and not existing.dead then existing:destroy() end

    local sc = {
        name    = name,
        dead    = false,
        conns   = {},
        insts   = {},
        threads = {},
        tweens  = {},
        gen     = BX.generation,
    }

    -- True while this scope AND this copy of the hub are both current. Every
    -- loop body should test it; sc:loop does that for you.
    function sc:alive()
        return (not self.dead) and BX.alive()
    end

    function sc:connect(signal, fn)
        if self.dead then return nil end
        local c = signal:Connect(fn)
        self.conns[#self.conns + 1] = c
        return c
    end

    function sc:own(inst)
        if self.dead then
            -- Created after the scope died: destroy it now rather than leak it.
            pcall(function() inst:Destroy() end)
            return inst
        end
        self.insts[#self.insts + 1] = inst
        return inst
    end

    -- A thread that is cancelled on destroy. The label is what shows up in the
    -- log if the body throws, so make it say which job this is.
    function sc:spawn(label, fn, ...)
        if self.dead then return nil end
        local th
        th = task.spawn(function(...)
            BX.try(self.name .. "/" .. label, fn, ...)
            -- Finished on its own: stop holding the thread reference so the
            -- closure and everything it captured can be collected.
            for i, t in ipairs(self.threads) do
                if t == th then table.remove(self.threads, i) break end
            end
        end, ...)
        self.threads[#self.threads + 1] = th
        return th
    end

    -- THE REPLACEMENT FOR `while true do ... end`.
    --
    -- Exits on its own when the scope dies or the hub is retired, so a disabled
    -- feature cannot keep a loop running and a re-execute cannot leave two of
    -- them fighting. A body that throws is logged once against its label and
    -- the loop keeps its cadence instead of dying silently.
    function sc:loop(label, interval, fn)
        return self:spawn(label .. "/loop", function()
            while self:alive() do
                BX.try(self.name .. "/" .. label, fn)
                if not self:alive() then return end
                task.wait(interval)
            end
        end)
    end

    -- A per-frame job. Same contract as :connect, but saying so at the call
    -- site is what lets the profiler time it: every handler registered here is
    -- measured and attributed by name, so the frame budget is known rather
    -- than argued about. See BX.profile.report().
    function sc:onFrame(label, signal, fn)
        local tag = self.name .. "/" .. label
        local guarded = BX.guard(tag, fn)
        local timed = BX.profile and BX.profile.wrap(tag, guarded) or guarded
        return self:connect(signal, timed)
    end

    function sc:delay(label, seconds, fn)
        if self.dead then return end
        task.delay(seconds, function()
            -- Checked at FIRE time, not at schedule time. A delay set up before
            -- a teardown must not fire into a destroyed feature - that is the
            -- "half of a feature still running" case.
            if not self:alive() then return end
            BX.try(self.name .. "/" .. label, fn)
        end)
    end

    function sc:tween(obj, t, props, style, dir)
        if self.dead then return nil end
        local tween
        BX.try(self.name .. "/tween", function()
            tween = BX.require("core.services").TweenService:Create(obj,
                TweenInfo.new(t, style or Enum.EasingStyle.Quint,
                    dir or Enum.EasingDirection.Out), props)
            tween:Play()
        end)
        if tween then self.tweens[#self.tweens + 1] = tween end
        return tween
    end

    function sc:destroy()
        if self.dead then return end
        self.dead = true

        for _, c in ipairs(self.conns) do pcall(function() c:Disconnect() end) end
        for _, t in ipairs(self.tweens) do pcall(function() t:Cancel() end) end
        for _, i in ipairs(self.insts) do pcall(function() i:Destroy() end) end
        -- NEVER CANCEL THE THREAD WE ARE STANDING ON.
        --
        -- A scope is very often destroyed from inside one of its own threads -
        -- a ticker that notices the feature was switched off and tears itself
        -- down. Cancelling the running thread there stops destroy() dead,
        -- half-done: connections dropped, Instances never destroyed. That is
        -- precisely the "one error leaves half a feature running" case, except
        -- it is not even an error.
        --
        -- The running thread is left alone. It is already unwinding, and
        -- self.dead is set, so any loop it is in exits at its next check.
        local me = coroutine.running()
        for _, th in ipairs(self.threads) do
            -- A thread blocked in task.wait is not reachable by its loop
            -- condition until it wakes; cancelling ends it now.
            if th ~= me then pcall(task.cancel, th) end
        end

        -- Drop every reference. Without this the scope table itself keeps the
        -- destroyed Instances, the dead threads and everything their closures
        -- captured alive for as long as the hub runs.
        self.conns, self.insts, self.threads, self.tweens = {}, {}, {}, {}

        if BX._scopes[self.name] == self then BX._scopes[self.name] = nil end
    end

    -- What the profiler and the audit command report on.
    function sc:counts()
        return {
            conns   = #self.conns,
            insts   = #self.insts,
            threads = #self.threads,
            tweens  = #self.tweens,
        }
    end

    BX._scopes[name] = sc
    return sc
end

-- Every live scope, smallest surface for "what is this hub currently holding".
function BX.scopeReport()
    local out = {}
    for name, sc in pairs(BX._scopes) do
        if not sc.dead then
            local c = sc:counts()
            out[#out + 1] = ("%-24s conns=%-3d insts=%-4d threads=%-3d tweens=%d")
                :format(name, c.conns, c.insts, c.threads, c.tweens)
        end
    end
    table.sort(out)
    return out
end

function BX.destroyAllScopes()
    for _, sc in pairs(BX._scopes) do
        pcall(function() sc:destroy() end)
    end
    BX._scopes = {}
end

--[[ ==== boot/03_profile.lua ========================================= ]]
-- =============================================================================
-- PROFILE: measure it, do not guess at it
-- =============================================================================
--
-- Every per-frame job registered through sc:onFrame is timed automatically, so
-- the frame budget is attributed to the feature that actually spends it rather
-- than argued about. Nothing here needs to be wired up by hand.
--
--     BX.profile.report()        -- one line per per-frame job, worst first
--     BX.profile.health()        -- memory, fps, scopes, connections, threads
--
-- THE LONG-SESSION CHECK. The sampler writes a health line into the trace file
-- every 60 seconds: memory, frame rate, live scope count, total connections and
-- total threads. On a healthy build those five numbers are flat across hours.
-- A leak shows up as a slow climb in connections or threads long before anyone
-- notices the frame rate, so the trace file answers "is it getting worse?"
-- without anybody having to sit and watch it.
--
-- Cost of the instrumentation itself: two os.clock() calls per handler per
-- frame. Set BX.profile.enabled = false to drop even that.

BX.profile = {
    enabled = true,
    _stats  = {},    -- label -> { n, total, max, last }
    _mem0   = nil,
    _t0     = os.clock(),
}

local P = BX.profile

-- WHAT WE OWN, TRACKED SEPARATELY FROM ROBLOX'S TOTAL.
--
-- Total process memory is mostly the game, and it oscillates by hundreds of MB
-- as the GC works - it can neither prove nor disprove a leak in the hub. A
-- module registers its caches here and the health line reports their sizes, so
-- "does anything grow with each steal or respawn" is answered by a number we
-- actually control rather than inferred from Roblox's heap.
--
--     BX.profile.watch("eggs.cache", function() return #list end)
P._watch = {}
function P.watch(name, fn) P._watch[name] = fn end
function P.watched()
    local out = {}
    for name, fn in pairs(P._watch) do
        local ok, n = pcall(fn)
        out[#out + 1] = ("%s=%s"):format(name, ok and tostring(n) or "?")
    end
    table.sort(out)
    return out
end

-- THE LIFE TIMELINE.
--
-- Marks are stamped at the points a cycle can die, each with the character's
-- health and humanoid state at that moment. When a run dies, the trace shows
-- WHICH mark it died after instead of leaving us to guess between the bait,
-- the anchor, the unanchor and the teleport.
P._marks = {}

function P.mark(name)
    local ok, health, state, swapped = pcall(function()
        local plr = game:GetService("Players").LocalPlayer
        local char = plr and plr.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if not hum then return -1, "no-humanoid", false end
        return hum.Health, tostring(hum:GetState()):gsub("Enum.HumanoidStateType.", ""),
               hum:GetAttribute("BlyxoStealHum") == true
    end)
    local row = {
        name = name, at = os.clock(),
        health = ok and health or -1,
        state = ok and state or "?",
        swapped = ok and swapped or false,
    }
    P._marks[#P._marks + 1] = row
    if #P._marks > 200 then table.remove(P._marks, 1) end
    return row
end

function P.marksSince(t)
    local out = {}
    for _, r in ipairs(P._marks) do
        if r.at >= (t or 0) then
            out[#out + 1] = ("%s@%.2f hp=%.0f %s%s"):format(
                r.name, r.at - (t or 0), r.health, r.state, r.swapped and " swapped" or "")
        end
    end
    return out
end

function P.wrap(label, fn)
    local s = P._stats[label]
    if not s then
        s = { n = 0, total = 0, max = 0, last = 0 }
        P._stats[label] = s
    end
    return function(...)
        if not P.enabled then return fn(...) end
        local t0 = os.clock()
        fn(...)
        local dt = os.clock() - t0
        s.n = s.n + 1
        s.total = s.total + dt
        s.last = dt
        if dt > s.max then s.max = dt end
    end
end

-- Worst first, in milliseconds. `avg` is the number that matters for a smooth
-- frame; `max` catches the one-off spike that shows up as a stutter.
function P.report()
    local rows = {}
    for label, s in pairs(P._stats) do
        if s.n > 0 then
            rows[#rows + 1] = {
                label = label,
                avg   = (s.total / s.n) * 1000,
                max   = s.max * 1000,
                total = s.total,
                n     = s.n,
            }
        end
    end
    table.sort(rows, function(a, b) return a.total > b.total end)

    local out = { ("%-34s %8s %8s %9s %8s"):format("per-frame job", "avg ms", "max ms", "total s", "calls") }
    for _, r in ipairs(rows) do
        out[#out + 1] = ("%-34s %8.3f %8.3f %9.2f %8d")
            :format(r.label, r.avg, r.max, r.total, r.n)
    end
    return out
end

local function memMb()
    local ok, v = pcall(function()
        return game:GetService("Stats"):GetTotalMemoryUsageMb()
    end)
    if ok and type(v) == "number" then return v end
    ok, v = pcall(gcinfo)
    return (ok and type(v) == "number") and (v / 1024) or 0
end

function P.health()
    local conns, threads, scopes, insts = 0, 0, 0, 0
    for _, sc in pairs(BX._scopes or {}) do
        if not sc.dead then
            scopes = scopes + 1
            local c = sc:counts()
            conns   = conns + c.conns
            insts   = insts + c.insts
            threads = threads + c.threads
        end
    end
    local mem = memMb()
    P._mem0 = P._mem0 or mem
    return {
        uptime  = os.clock() - P._t0,
        mem     = mem,
        memGrow = mem - P._mem0,
        scopes  = scopes,
        conns   = conns,
        insts   = insts,
        threads = threads,
        loaded  = (function() local n = 0 for _ in pairs(BX._loaded) do n = n + 1 end return n end)(),
    }
end

function P.start()
    local sc  = BX.scope("boot.profile")
    local log = BX.require("boot.log").for_module("profile")
    local fps, frames, last = 0, 0, os.clock()

    sc:connect(BX.require("core.services").RunService.Heartbeat, function()
        frames = frames + 1
    end)

    sc:loop("health", 60, function()
        local now = os.clock()
        fps, frames, last = frames / math.max(now - last, 0.001), 0, now
        local h = P.health()
        -- One line, parseable, in the trace file. Flat numbers across a long
        -- session mean a clean build; a climb in conns or threads is a leak.
        local w = P.watched()
        log.info("health up=%.0fs fps=%.0f mem=%.0fMB (%+.0f) scopes=%d conns=%d insts=%d threads=%d%s",
            h.uptime, fps, h.mem, h.memGrow, h.scopes, h.conns, h.insts, h.threads,
            #w > 0 and (" | " .. table.concat(w, " ")) or "")
    end)

    return sc
end

--[[ ==== core/services.lua =========================================== ]]
-- Cached game services. Resolved once, in one place.
--
-- The old build called game:GetService in dozens of scattered spots, including
-- inside per-frame loops. GetService is cheap but not free, and more to the
-- point it meant no single place to see what the hub actually touches.
BX.module("core.services", function(BX)
    local log = BX.require("boot.log").for_module("services")
    local M = {}

    local WANTED = {
        "Players", "ReplicatedStorage", "RunService", "TweenService",
        "UserInputService", "Lighting", "Workspace", "HttpService",
        "CoreGui", "TextService", "Stats",
        -- Missing from this list until 2026-09-12: features/misc/servers.lua
        -- called svc.TeleportService:TeleportToPlaceInstance inside a pcall,
        -- so every server hop threw "attempt to index nil" and was logged as
        -- "teleport refused" - the buttons never worked on any build.
        "TeleportService",
    }

    for _, name in ipairs(WANTED) do
        local ok, svc = pcall(game.GetService, game, name)
        if ok and svc then
            M[name] = svc
        else
            -- Missing service is a real, reportable condition - not something
            -- to discover later as a nil index deep inside a feature.
            log.error("service unavailable: %s", name)
        end
    end

    -- WAIT FOR THE PLAYER, BOUNDED. An executor that runs auto-execute
    -- scripts before the client has finished joining sees Players.LocalPlayer
    -- as nil, and every module that captures svc.LocalPlayer at load then
    -- holds nil for the session - remotes, the character, ESP, all of it.
    -- Ten seconds is far more than a join takes; a genuine nil after that is
    -- reported by the services stage as the required failure it is.
    if M.Players and not M.Players.LocalPlayer then
        local deadline = os.clock() + 10
        while not M.Players.LocalPlayer and os.clock() < deadline do task.wait(0.1) end
        if M.Players.LocalPlayer then
            log.info("LocalPlayer arrived late (%.1fs) - waited for it", 10 - (deadline - os.clock()))
        else
            log.error("Players.LocalPlayer is still nil after 10s")
        end
    end
    M.LocalPlayer = M.Players and M.Players.LocalPlayer
    return M
end)

--[[ ==== core/net.lua ================================================ ]]
-- =============================================================================
-- CORE.NET: the game's own remotes, called safely
-- =============================================================================
--
--     local net = BX.require("core.net")
--     local ok, msg = net.call("RF/Treadmill/AskDoff")
--     local rf      = net.find("RF/Rift/AskState")
--
-- Every remote in this game lives in ReplicatedStorage.Packages.Networking with
-- its full name as the Instance name - "RF/Treadmill/AskDoff", not a nested
-- folder path. That container is resolved once and held.
--
-- WHY THIS IS ITS OWN MODULE NOW.
--
-- features/treadmill.lua carried this privately with a note saying "kept local
-- because this is currently the only caller; it moves to core/ the moment a
-- second feature needs it". Farm needs it twice - the treadmill hold and Equip
-- Best Pets - so it moves, rather than being copied a third time.
--
-- A MISSING REMOTE IS A RESULT, NOT AN ERROR.
--
-- The game renames and removes endpoints between updates. Every call returns
-- false plus a reason instead of throwing, so a feature built on an endpoint
-- that has gone away degrades to "refused" and says which name it wanted -
-- which is the difference between a legible failure and a silent one.

BX.module("core.net", function(BX)
    local svc = BX.require("core.services")
    local log = BX.require("boot.log").for_module("net")

    local M = {}

    local container, containerAt = nil, 0
    local CONTAINER_TTL = 30

    local function networking()
        local now = os.clock()
        if container and container.Parent and (now - containerAt) < CONTAINER_TTL then
            return container
        end
        local pkgs = svc.ReplicatedStorage:FindFirstChild("Packages")
        local net = pkgs and pkgs:FindFirstChild("Networking")
        container, containerAt = net, now
        return net
    end

    -- The remote Instance, or nil. Callers that need to connect to a RemoteEvent
    -- want this; callers that just want an answer want call().
    function M.find(name)
        local net = networking()
        return net and net:FindFirstChild(name) or nil
    end

    -- Returns the remote's own first return value, or false plus a reason. A
    -- refusal from the server and a missing endpoint are both "false, why".
    function M.call(name, ...)
        local rf = M.find(name)
        if not rf then return false, "remote not found: " .. tostring(name) end
        local ok, a, b = pcall(function(...) return rf:InvokeServer(...) end, ...)
        if not ok then return false, tostring(a) end
        return a, b
    end

    -- Fire-and-forget for a RemoteEvent.
    function M.fire(name, ...)
        local re = M.find(name)
        if not re then return false, "remote not found: " .. tostring(name) end
        local ok, err = pcall(function(...) re:FireServer(...) end, ...)
        if not ok then return false, tostring(err) end
        return true
    end

    return M
end)

--[[ ==== core/data.lua =============================================== ]]
-- =============================================================================
-- CORE.DATA: the game's own data modules, resolved once and correctly
-- =============================================================================
--
--     local data = BX.require("core.data")
--     data.assets()        -- Data.Assets   .Directory keyed by AssetCategory
--     data.areas()         -- Data.Areas    .Directory keyed by area id
--     data.eggState()      -- Client.EggState
--     data.assetEarnings() -- Shared.Util.AssetEarnings
--     data.plotState()     -- PlotState
--
-- WHY THIS EXISTS, AND IT IS NOT TIDINESS.
--
-- Three modules had their own private copy of:
--
--     local found = ReplicatedStorage:FindFirstChild(name, true)
--     return found and found:IsA("ModuleScript") and require(found) or nil
--
-- and for one name that is silently wrong. There is a FOLDER called
-- ReplicatedStorage.Assets and a MODULESCRIPT called ReplicatedStorage.Data.Assets.
-- A recursive FindFirstChild returns the folder - it is a direct child, so it is
-- reached first - the IsA check then fails, and the function returns nil without
-- ever looking further.
--
-- Measured on the live game before this fix, on every egg in the field:
--
--     Red Panda        rarity=?  kg=0  value=1607568  area=Cherry Blossom
--     Cyclops Gorilla  rarity=?  kg=0  value=1480986  area=Cosmic
--     Blade Head       rarity=?  kg=0  value=605224   area=Titan Temple
--
-- So egg RARITY and WEIGHT have never worked in this rebuild. Values and names
-- come from elsewhere and were fine, which is exactly why it went unnoticed:
-- nothing on screen depended on rarity until the Farm tab's filters.
--
-- V3.1 does not have this bug because it looks up a PATH - needModule(RS, "Data",
-- "Assets") - rather than a bare name.
--
-- THE RULE HERE: PATH FIRST, THEN A SEARCH THAT KEEPS LOOKING.
--
-- Each accessor names the path it expects. If the path moves, the fallback walks
-- the descendants for a ModuleScript of that name and, crucially, does not stop
-- at the first Instance that merely shares the name. Resolved once and held;
-- a miss is logged once, loudly, rather than becoming a "?" on screen.

BX.module("core.data", function(BX)
    local svc  = BX.require("core.services")
    local exec = BX.require("core.exec")
    local log  = BX.require("boot.log").for_module("data")

    local M = {}

    local cache = {}      -- key -> { mod = <required value> } or { missing = true }

    local function atPath(...)
        local node = svc.ReplicatedStorage
        for _, part in ipairs({ ... }) do
            if not node then return nil end
            node = node:FindFirstChild(part)
        end
        return node
    end

    -- A ModuleScript of this name ANYWHERE, ignoring same-named folders.
    local function searchModule(name)
        for _, d in ipairs(svc.ReplicatedStorage:GetDescendants()) do
            if d:IsA("ModuleScript") and d.Name == name then return d end
        end
        return nil
    end

    -- key: what to remember it as. path: where it is expected to be.
    local function resolve(key, path)
        local held = cache[key]
        if held then return held.mod end

        -- NO require() ON THIS EXECUTOR = NO WALK. Probed once by core.exec;
        -- without it there is nothing a descendants search could change, so
        -- the miss is recorded once, with the executor's own error.
        if not exec.can.gameRequire then
            log.error("cannot require game modules on this executor (%s) - %s unavailable",
                tostring(exec.gameRequireWhy), path[#path])
            cache[key] = { missing = true }
            return nil
        end

        local name = path[#path]
        local inst = atPath(table.unpack(path))
        if not (inst and inst:IsA("ModuleScript")) then
            inst = searchModule(name)
            if inst then
                log.warn("%s was not at %s - found it at %s",
                    name, table.concat(path, "."), inst:GetFullName())
            end
        end

        if not inst then
            cache[key] = { missing = true }
            log.error("could not resolve the game module %s (expected %s)",
                name, table.concat(path, "."))
            return nil
        end

        local mod
        local ok = BX.try("data.require." .. key, function() mod = require(inst) end)
        if not ok or type(mod) ~= "table" then
            cache[key] = { missing = true }
            log.error("%s could not be required", inst:GetFullName())
            return nil
        end

        cache[key] = { mod = mod }
        return mod
    end

    function M.assets()        return resolve("assets", { "Data", "Assets" }) end
    function M.areas()         return resolve("areas", { "Data", "Areas" }) end
    function M.eggState()      return resolve("eggState", { "Client", "EggState" }) end
    function M.assetEarnings() return resolve("assetEarnings", { "Shared", "Util", "AssetEarnings" }) end
    function M.plotState()     return resolve("plotState", { "Client", "PlotState" }) end
    function M.slotIdentity()  return resolve("slotIdentity", { "Shared", "Util", "AreaEggSlotIdentity" }) end

    -- .Directory is the shape both Assets and Areas share; nil when the module
    -- is missing, so callers degrade rather than throw.
    function M.assetsDir()
        local a = M.assets()
        return a and a.Directory or nil
    end

    function M.areasDir()
        local a = M.areas()
        return a and a.Directory or nil
    end

    -- What resolved and what did not, for the audit.
    function M.report()
        local out = {}
        for key, held in pairs(cache) do
            out[#out + 1] = key .. (held.missing and "=MISSING" or "=ok")
        end
        table.sort(out)
        return out
    end

    return M
end)

--[[ ==== core/exec.lua =============================================== ]]
-- =============================================================================
-- CORE.EXEC: what this executor can actually do
-- =============================================================================
--
-- Every executor-specific function is probed ONCE, here, and reached through a
-- wrapper that has a fallback or a clean "no". Nothing else in the hub calls
-- writefile, getcustomasset, setclipboard, getgc or request directly.
--
--     local exec = BX.require("core.exec")
--
--     if exec.can.files then ... end          -- capability flags
--     exec.writeFile(path, data)              -- false when unsupported
--     exec.readFile(path)                     -- nil when unsupported
--     exec.clipboard(text)                    -- tries four spellings
--     exec.httpRequest{ Url = ..., ... }      -- nil when unsupported
--     exec.gcScan()                           -- {} when unsupported
--
-- WHY THIS EXISTS AT ALL.
--
-- "It works on mine" is the single most expensive assumption in a script like
-- this. Executors disagree about which of these exist, what they are called,
-- what they return and whether they throw. V3.1 called them inline, in dozens
-- of places, each with its own ad-hoc pcall - so a missing function did not
-- disable one feature, it produced a different failure every place it was
-- touched, and mobile executors (which support the least) got the worst of it.
--
-- The rule: a missing capability disables THAT capability, never the hub.
--
-- Nothing here throws. Every function returns a value the caller can carry on
-- from, and the one-time report in the log says what this machine actually has,
-- which is the first thing worth knowing about any bug report.

BX.module("core.exec", function(BX)
    local log = BX.require("boot.log").for_module("exec")

    local M = {}

    -- SIMULATED DENIES, FOR TESTING THE DEGRADED PATHS ON A FULL EXECUTOR.
    --
    --     getgenv().BLYXO_CAPS_DENY = { prompts = true, files = true }
    --
    -- before executing makes those capabilities probe as missing, so "what
    -- does the hub do on an executor without fireproximityprompt" can be
    -- answered here instead of guessed. Reported in the diag line so a trace
    -- from a simulated run can never be mistaken for a real one.
    local env = (type(getgenv) == "function" and getgenv()) or _G
    local deny = type(env.BLYXO_CAPS_DENY) == "table" and env.BLYXO_CAPS_DENY or {}
    M.simulatedDenies = deny

    -- WHERE EXECUTOR GLOBALS ACTUALLY LIVE, AND WHY FOUR LOOKUPS.
    --
    -- The first cut read getfenv()[name], then _G[name], then rawget on
    -- getfenv(0). On an executor where getfenv is sandboxed or absent that is
    -- three misses in a row - executor functions are not in _G, and rawget
    -- skips the __index chain they are reached through - so EVERY capability
    -- probed as missing: files, request, prompts, clipboard. The hub then
    -- said "Config saving is not supported", sent no webhooks, refused
    -- server hops and refused prompt steals on a machine that had all of
    -- them. getgenv() is the executor's own global table and is the first
    -- place to look; a compiled `return <name>` is the last, because
    -- loadstring resolves the name exactly the way the executor's own
    -- scripts do.
    local function fn(name)
        if deny[name] then return nil end
        local ok, v
        ok, v = pcall(function() return type(getgenv) == "function" and getgenv()[name] or nil end)
        if not ok or type(v) ~= "function" then
            ok, v = pcall(function() return getfenv and getfenv()[name] or nil end)
        end
        if not ok or type(v) ~= "function" then
            ok, v = pcall(function() return (_G and _G[name]) end)
        end
        if not ok or type(v) ~= "function" then
            ok, v = pcall(function()
                local chunk = loadstring and loadstring("return " .. name)
                return chunk and chunk() or nil
            end)
        end
        return (ok and type(v) == "function") and v or nil
    end

    -- Resolved once. Names vary between executors, so each is a list of
    -- spellings in preference order.
    local function first(...)
        for _, name in ipairs({ ... }) do
            local f = fn(name)
            if f then return f, name end
        end
        return nil, nil
    end

    local f_writefile   = first("writefile")
    local f_readfile    = first("readfile")
    local f_isfile      = first("isfile")
    local f_delfile     = first("delfile")
    local f_isfolder    = first("isfolder")
    local f_makefolder  = first("makefolder")
    local f_listfiles   = first("listfiles")
    local f_customasset = first("getcustomasset", "getsynasset")
    local f_gethui      = first("gethui")
    local f_getgc       = first("getgc")
    local f_getconns    = first("getconnections")
    local f_hookfn      = first("hookfunction", "replaceclosure")
    local f_getrawmeta  = first("getrawmetatable")
    local f_setreadonly = first("setreadonly", "make_writeable")
    local f_queueport   = first("queue_on_teleport", "queueonteleport")
    local f_identify    = first("identifyexecutor", "getexecutorname")
    local f_fireprompt  = first("fireproximityprompt")

    local f_clip, clipName = first("setclipboard", "toclipboard", "set_clipboard", "setrbxclipboard")

    -- CAN THIS EXECUTOR require() THE GAME'S OWN ModuleScripts?
    --
    -- Everything that names an egg, prices it, or reads the field goes
    -- through require(ReplicatedStorage.<...>). Some executors refuse that
    -- outright, some only for non-public modules, and core.data used to
    -- discover it one module at a time with a descendants walk per miss.
    -- Probed once, on the first ModuleScript ReplicatedStorage has, so the
    -- answer is known before any feature asks.
    local canRequire, requireWhy = false, "no ModuleScript to probe"
    do
        local ok, err = pcall(function()
            local RS = game:GetService("ReplicatedStorage")
            local probe = RS:FindFirstChildWhichIsA("ModuleScript", true)
            if not probe then return end
            local r = require(probe)
            canRequire, requireWhy = true, probe:GetFullName()
        end)
        if not ok then requireWhy = tostring(err) end
        if deny.gameRequire then canRequire, requireWhy = false, "simulated deny" end
    end

    -- syn.request lives on a table rather than as a global, so it is probed
    -- separately before the plain spellings.
    local f_request, requestName
    do
        local ok, v = pcall(function() return syn and syn.request end)
        if ok and type(v) == "function" then
            f_request, requestName = v, "syn.request"
        else
            ok, v = pcall(function() return http and http.request end)
            if ok and type(v) == "function" then
                f_request, requestName = v, "http.request"
            else
                f_request, requestName = first("request", "http_request", "httprequest")
            end
        end
    end

    -- Files need the whole set, not just writefile: a partial implementation
    -- (write but no read, or no isfile) is worse than none, because caching
    -- logic then cannot tell a cached file from a missing one.
    M.can = {
        files      = (f_writefile and f_readfile and f_isfile) and true or false,
        folders    = (f_isfolder and f_makefolder) and true or false,
        listFiles  = f_listfiles and true or false,
        customAsset = f_customasset and true or false,
        hiddenUi   = f_gethui and true or false,
        gc         = f_getgc and true or false,
        connections = f_getconns and true or false,
        hooking    = (f_hookfn and f_getrawmeta) and true or false,
        clipboard  = f_clip and true or false,
        request    = f_request and true or false,
        teleportQueue = f_queueport and true or false,
        -- The prompt steal. fireproximityprompt is the fast path; without it
        -- the prompt is held through ProximityPrompt:InputHoldBegin/End,
        -- which is Roblox's own client API and exists everywhere. So this is
        -- true on every executor - `promptVia` says which path.
        prompts    = true,
        gameRequire = canRequire,
    }
    M.promptVia = f_fireprompt and "fireproximityprompt" or "InputHoldBegin"
    M.gameRequireWhy = requireWhy

    M.name = "unknown"
    if f_identify then
        local ok, n = pcall(f_identify)
        if ok and type(n) == "string" and #n > 0 then M.name = n end
    end

    ---------- wrappers ----------
    -- Every one of these is safe to call unconditionally.

    function M.hiddenParent()
        if f_gethui then
            local ok, ui = pcall(f_gethui)
            if ok and ui then return ui end
        end
        return BX.require("core.services").CoreGui
    end

    function M.writeFile(path, data)
        if not f_writefile then return false end
        return (BX.try("exec.writeFile", f_writefile, path, data))
    end

    function M.readFile(path)
        if not f_readfile then return nil end
        local ok, data = BX.try("exec.readFile", f_readfile, path)
        return ok and data or nil
    end

    function M.isFile(path)
        if not f_isfile then return false end
        local ok, yes = pcall(f_isfile, path)
        return ok and yes or false
    end

    -- nil when the executor cannot list a folder (some have files but no
    -- listfiles); an empty table when it can and the folder is empty.
    function M.listFiles(path)
        if not f_listfiles then return nil end
        local ok, files = BX.try("exec.listFiles", f_listfiles, path)
        if not ok or type(files) ~= "table" then return nil end
        return files
    end

    function M.deleteFile(path)
        if not f_delfile then return false end
        return (BX.try("exec.deleteFile", f_delfile, path))
    end

    -- Creates every level, because not all executors create parents for you.
    function M.ensureFolder(path)
        if not M.can.folders then return false end
        local built = ""
        for part in tostring(path):gmatch("[^/]+") do
            built = (built == "") and part or (built .. "/" .. part)
            local ok, exists = pcall(f_isfolder, built)
            if ok and not exists then
                if not BX.try("exec.makeFolder", f_makefolder, built) then return false end
            end
        end
        return true
    end

    function M.customAsset(path)
        if not f_customasset then return nil end
        local ok, id = BX.try("exec.customAsset", f_customasset, path)
        return ok and id or nil
    end

    -- Tries every spelling, not just the one this executor advertises: some
    -- expose the name but throw, which is only discoverable by calling it.
    function M.clipboard(text)
        for _, name in ipairs({ "setclipboard", "toclipboard", "set_clipboard", "setrbxclipboard" }) do
            local f = fn(name)
            if f and pcall(f, text) then return true end
        end
        return false
    end

    function M.httpRequest(opts)
        if not f_request then return nil end
        local ok, res = BX.try("exec.httpRequest", f_request, opts)
        return ok and res or nil
    end

    -- THE EXPENSIVE ONE. A getgc(true) sweep walks every live object - V3.1
    -- measured ~196,000 objects and 43ms on a desktop, and a phone is far
    -- worse. It is never called casually: callers must cache the result and
    -- re-sweep only when something has actually invalidated it.
    function M.gcScan(tablesOnly)
        if not f_getgc then return {} end
        local t0 = os.clock()
        local ok, objs = BX.try("exec.gcScan", f_getgc, tablesOnly and true or false)
        if not ok or type(objs) ~= "table" then return {} end
        local ms = (os.clock() - t0) * 1000
        M.lastGcMs = ms
        -- Loud on purpose. If this shows up often in a trace, something is
        -- sweeping the heap in a loop and that is always a bug.
        log.warn("gc sweep: %d objects in %.0fms", #objs, ms)
        return objs
    end

    -- The steal path. V3.1 called fireproximityprompt inline, so an executor
    -- without it produced a different failure at each call site instead of one
    -- clear "this executor cannot do prompt steals".
    function M.firePrompt(prompt, holdDuration)
        if f_fireprompt then
            return (BX.try("exec.firePrompt", f_fireprompt, prompt, holdDuration or 0))
        end
        -- THE FALLBACK IS THE ENGINE'S OWN API. InputHoldBegin starts the
        -- hold exactly as a key press does; after HoldDuration the prompt
        -- fires Triggered and InputHoldEnd releases it. Yields for the hold,
        -- which the callers already allow for (they pass the duration).
        return (BX.try("exec.firePrompt.hold", function()
            prompt:InputHoldBegin()
            local hold = tonumber(holdDuration)
            if hold == nil then hold = tonumber(prompt.HoldDuration) or 0 end
            if hold > 0 then task.wait(hold + 0.05) end
            prompt:InputHoldEnd()
        end))
    end

    function M.report()
        local have, missing = {}, {}
        for k, v in pairs(M.can) do
            table.insert(v and have or missing, k)
        end
        table.sort(have); table.sort(missing)
        local denied = {}
        for k in pairs(deny) do denied[#denied + 1] = tostring(k) end
        table.sort(denied)
        return {
            executor = M.name,
            have = have,
            missing = missing,
            denied = denied,
            promptVia = M.promptVia,
            gameRequireWhy = requireWhy,
        }
    end

    local r = M.report()
    log.info("executor=%s clipboard=%s request=%s prompts=%s gameRequire=%s (%s)",
        M.name, tostring(clipName), tostring(requestName), M.promptVia,
        tostring(canRequire), tostring(requireWhy))
    if #r.denied > 0 then
        log.warn("SIMULATED capability denies active: %s", table.concat(r.denied, ", "))
    end
    log.info("supported: %s", #r.have > 0 and table.concat(r.have, ", ") or "(none)")
    if #r.missing > 0 then
        -- Not an error. It is the single most useful line in a bug report from
        -- a machine that is not yours.
        log.warn("unsupported here: %s", table.concat(r.missing, ", "))
    end

    return M
end)

--[[ ==== core/device.lua ============================================= ]]
-- =============================================================================
-- CORE.DEVICE: what this machine can afford
-- =============================================================================
--
--     local dev = BX.require("core.device")
--
--     dev.tier          -- "low" | "mid" | "high", updated live
--     dev.isTouch       -- phone or tablet
--     dev.scale(0.25)   -- an interval, stretched for weaker machines
--     dev.budget(12)    -- a per-pass work allowance, cut for weaker machines
--     dev.onTier(sc, fn)-- called when the tier changes
--
-- WHY A TIER AND NOT A CONSTANT.
--
-- A loop tuned on a 144fps desktop is a different loop on a phone. V3.1 has
-- the scars: a mover that discarded any frame longer than 50ms silently halved
-- travel speed at 10fps, and a steal loop that never yielded froze phones and
-- emulators hardest. Numbers picked on one machine are not portable.
--
-- The tier is measured, not assumed - a "desktop" running at 22fps under a
-- recording program is a low-tier machine that day, and a modern tablet is not
-- a phone just because it has a touchscreen.
--
-- It is also HYSTERETIC and slow to move, for the same reason the stats colours
-- are: a tier that flips every few seconds would have features rebuilding
-- themselves constantly, which is itself a cost.

BX.module("core.device", function(BX)
    local svc = BX.require("core.services")
    local cfg = BX.require("core.config")
    local log = BX.require("boot.log").for_module("device")

    local M = {}

    M.isTouch = svc.UserInputService.TouchEnabled
        and not svc.UserInputService.KeyboardEnabled

    -- Screen size separates a phone from a tablet. A tablet is a PC-sized
    -- screen and generally a PC-sized budget; treating every touch device as a
    -- phone is what made V3.1's UI look wrong on iPads.
    local function shortSide()
        local cam = workspace.CurrentCamera
        local vp = cam and cam.ViewportSize
        if not vp or vp.Y < 10 then return 1080 end
        return math.min(vp.X, vp.Y)
    end
    M.smallScreen = shortSide() < 500

    -- Starting guess, replaced by measurement within a few seconds. A phone
    -- starts pessimistic so the first seconds of load are not the heaviest.
    M.tier = (M.isTouch and M.smallScreen) and "low" or "mid"
    M.fps = nil

    local MULT = { low = 2.2, mid = 1.35, high = 1.0 }

    -- An interval in seconds, stretched on weaker machines. A scan that is
    -- reasonable four times a second on a desktop is not on a phone, and the
    -- phone is exactly where the extra work turns into a stutter.
    function M.scale(seconds)
        return seconds * (MULT[M.tier] or 1.35)
    end

    -- A per-pass work allowance (how many things to build/check this pass),
    -- cut on weaker machines. Never returns less than 1: a budget of zero is a
    -- feature that silently does nothing.
    function M.budget(n)
        local share = (M.tier == "low" and 0.35) or (M.tier == "mid" and 0.7) or 1
        return math.max(1, math.floor(n * share + 0.5))
    end

    -- True when the machine is struggling badly enough that optional work
    -- should be skipped entirely this pass.
    function M.lite()
        return M.tier == "low"
    end

    local listeners = {}
    function M.onTier(sc, label, fn)
        listeners[#listeners + 1] = { scope = sc, label = label, fn = fn }
    end

    local function setTier(t)
        if M.tier == t then return end
        local was = M.tier
        M.tier = t
        log.info("tier %s -> %s (fps %.0f, touch=%s, short=%d)",
            was, t, M.fps or -1, tostring(M.isTouch), shortSide())
        for i = #listeners, 1, -1 do
            local L = listeners[i]
            if not L.scope or L.scope.dead then
                table.remove(listeners, i)
            else
                BX.try("device/" .. L.label, L.fn, t, was)
            end
        end
    end

    -- MEASURED, WITH HYSTERESIS AND A HOLD.
    --
    -- Same reasoning as the stats colours: one threshold means a machine
    -- sitting on it changes tier constantly. A tier also has to hold its new
    -- reading for two consecutive samples before it is adopted, so a single
    -- bad stretch (an asset load, a teleport) does not re-tier the hub.
    local sc = BX.scope("core.device")
    local frames = 0
    sc:connect(svc.RunService.Heartbeat, function() frames = frames + 1 end)

    local pending, pendingCount = nil, 0
    sc:loop("measure", 5, function()
        local fps = frames / 5
        frames = 0
        M.fps = M.fps and (M.fps + (fps - M.fps) * 0.4) or fps

        local want = M.tier
        if M.tier == "high" then
            if M.fps < 45 then want = "mid" end
        elseif M.tier == "mid" then
            if M.fps < cfg.LITE_FPS then want = "low"
            elseif M.fps > 75 then want = "high" end
        else
            if M.fps > 40 then want = "mid" end
        end

        -- A phone is never promoted to high, whatever it momentarily reports.
        -- A 120Hz phone can post excellent numbers while idle and still stall
        -- the moment real work starts.
        if want == "high" and M.isTouch and M.smallScreen then want = "mid" end

        if want == M.tier then
            pending, pendingCount = nil, 0
            return
        end
        if pending == want then
            pendingCount = pendingCount + 1
        else
            pending, pendingCount = want, 1
        end
        if pendingCount >= 2 then
            setTier(want)
            pending, pendingCount = nil, 0
        end
    end)

    log.info("start tier=%s touch=%s smallScreen=%s", M.tier,
        tostring(M.isTouch), tostring(M.smallScreen))

    return M
end)

--[[ ==== core/character.lua ========================================== ]]
-- =============================================================================
-- CORE.CHARACTER: one place that knows about the character
-- =============================================================================
--
-- Respawning is where duplicate state comes from. Every feature that cared
-- about the character used to connect its own CharacterAdded handler, and each
-- respawn added another one - so after five deaths, five handlers rebuilt five
-- copies of the same thing, and every one of them held a reference to a
-- character that no longer existed.
--
-- There is one CharacterAdded connection in the hub, and it lives here.
--
--     local ch = BX.require("core.character")
--
--     ch.get()                        -- current character, or nil
--     ch.root()                       -- HumanoidRootPart, or nil
--     ch.humanoid()                   -- Humanoid, or nil
--     ch.onSpawn(sc, "label", fn)     -- fn(character) now and on every respawn
--
-- onSpawn takes the SCOPE that owns the callback, so a feature switched off
-- stops being called back - without that, a disabled feature quietly wakes up
-- again on the next respawn.

BX.module("core.character", function(BX)
    local svc = BX.require("core.services")
    local log = BX.require("boot.log").for_module("character")

    local M = {}
    local plr = svc.LocalPlayer

    -- Weak values: when the character is destroyed, nothing here is the reason
    -- it stays in memory. A strong reference to an old character (and through
    -- it, every part, every Animator, every track) is one of the largest single
    -- leaks a hub of this kind can have.
    local current = setmetatable({}, { __mode = "v" })

    local listeners = {}   -- { scope = sc, label = str, fn = fn }

    function M.get()
        local c = current.char
        -- A character removed from the DataModel is as good as gone, even if
        -- something else is still holding it.
        if c and c.Parent then return c end
        return plr and plr.Character
    end

    function M.root()
        local c = M.get()
        return c and c:FindFirstChild("HumanoidRootPart")
    end

    function M.humanoid()
        local c = M.get()
        return c and c:FindFirstChildOfClass("Humanoid")
    end

    local function fire(char)
        current.char = char
        -- Backwards over the list, so a listener whose scope died can be
        -- dropped as we go without disturbing the iteration.
        for i = #listeners, 1, -1 do
            local L = listeners[i]
            if not L.scope or L.scope.dead then
                table.remove(listeners, i)
            else
                BX.try(("character/%s"):format(L.label), L.fn, char)
            end
        end
    end

    function M.onSpawn(sc, label, fn)
        listeners[#listeners + 1] = { scope = sc, label = label, fn = fn }
        -- Already alive: call it now, so a feature enabled mid-life does not
        -- sit idle until the next death.
        local c = M.get()
        if c then BX.try(("character/%s"):format(label), fn, c) end
    end

    -- THE ONE CONNECTION. Owned by a scope of its own so a re-execute retires
    -- it along with everything else.
    local sc = BX.scope("core.character")
    if plr then
        sc:connect(plr.CharacterAdded, function(char)
            log.trace("respawn")
            -- Wait for the root part before telling anyone: a feature that
            -- starts moving a character with no HumanoidRootPart throws, and
            -- that throw used to take the rest of the respawn handling with it.
            task.spawn(function()
                BX.try("character/wait", function()
                    char:WaitForChild("HumanoidRootPart", 10)
                end)
                if BX.alive() then fire(char) end
            end)
        end)
        sc:connect(plr.CharacterRemoving, function()
            current.char = nil
        end)
        current.char = plr.Character
    else
        log.error("no LocalPlayer - character tracking unavailable")
    end

    M._listenerCount = function() return #listeners end
    return M
end)

--[[ ==== core/restore.lua ============================================ ]]
-- =============================================================================
-- CORE.RESTORE: whatever you change, register how to change it back
-- =============================================================================
--
--     local rs = BX.require("core.restore")
--
--     rs.remember("movement.walkspeed",
--         function() return hum.WalkSpeed end,          -- read
--         function(v) hum.WalkSpeed = v end)            -- write
--     hum.WalkSpeed = 1620                              -- now modify freely
--
--     rs.restoreAll()      -- every terminal path calls this. Idempotent.
--     rs.audit()           -- what is STILL different from what we captured
--
-- WHY A REGISTRY AND NOT "EACH FEATURE REMEMBERS".
--
-- Each feature remembering is what we had, and it failed in the most ordinary
-- way: movement.reset() put WalkSpeed back to a HARDCODED 16 because there was
-- no anticheat adapter to ask for the real value. Measured on a live character
-- whose actual WalkSpeed was 209.8, so every run left the player crawling.
-- That is the "my character is so slow now" report, and no amount of care at
-- the call site would have caught it - the call site did not know the original.
--
-- Here the original is captured at the moment of the FIRST change, by the code
-- that is about to make it, and nothing else ever has to know what it was.
--
-- CAPTURED ONCE PER KEY. A second remember() for a key already held is
-- ignored, so a value modified repeatedly during a run still restores to what
-- it was before the run - not to what it was halfway through.
--
-- TIED TO THE CHARACTER. A respawn replaces the Humanoid, so values captured
-- against the old one are meaningless and are dropped rather than written back
-- onto a new character that never had them.

BX.module("core.restore", function(BX)
    local ch  = BX.require("core.character")
    local log = BX.require("boot.log").for_module("restore")

    local M = {}

    local entries = {}     -- key -> { read, write, original, char, at }
    local order = {}       -- keys, in the order they were first captured

    BX.profile.watch("restore.pending", function() return #order end)

    -- Capture the current value under `key`, if it is not already held.
    function M.remember(key, read, write)
        if entries[key] then return false end
        local ok, value = pcall(read)
        if not ok then
            log.warn("could not read %s to remember it: %s", key, tostring(value))
            return false
        end
        entries[key] = {
            read = read, write = write, original = value,
            char = ch.get(), at = os.clock(),
        }
        order[#order + 1] = key
        return true
    end

    -- For things with no single property to read back - a destroyed instance, a
    -- patched function - register an undo directly.
    function M.onRestore(key, undo)
        if entries[key] then return false end
        entries[key] = { undo = undo, char = ch.get(), at = os.clock() }
        order[#order + 1] = key
        return true
    end

    -- Something we changed that CANNOT be undone. Recorded so the audit can
    -- report it honestly rather than the cleanup silently passing.
    function M.permanent(key, why)
        if entries[key] then return false end
        entries[key] = { permanent = why or "not reversible", char = ch.get() }
        order[#order + 1] = key
        return true
    end

    -- IDEMPOTENT. Calling it twice is harmless: each entry is removed as it is
    -- restored, so the second call has nothing left to do.
    function M.restoreAll()
        local restored, skipped, failed = 0, 0, 0
        local liveChar = ch.get()

        for i = #order, 1, -1 do
            local key = order[i]
            local e = entries[key]
            if e then
                if e.permanent then
                    skipped = skipped + 1
                elseif e.char and e.char ~= liveChar then
                    -- Captured against a character that no longer exists. The
                    -- new one never had this value; writing it back would be
                    -- inventing state, not restoring it.
                    skipped = skipped + 1
                else
                    local ok, err = pcall(function()
                        if e.undo then e.undo() else e.write(e.original) end
                    end)
                    if ok then
                        restored = restored + 1
                    else
                        failed = failed + 1
                        log.error("restoring %s failed: %s", key, tostring(err))
                    end
                end
                entries[key] = nil
            end
            table.remove(order, i)
        end

        return restored, skipped, failed
    end

    -- What is still different from what we captured. Called AFTER restoreAll,
    -- it should come back empty - that is the cleanup PASS.
    function M.audit()
        local diffs = {}
        for _, key in ipairs(order) do
            local e = entries[key]
            if e and e.read then
                local ok, now = pcall(e.read)
                if ok and tostring(now) ~= tostring(e.original) then
                    diffs[#diffs + 1] = ("%s: %s (was %s)")
                        :format(key, tostring(now), tostring(e.original))
                end
            elseif e and e.permanent then
                diffs[#diffs + 1] = ("%s: %s"):format(key, e.permanent)
            end
        end
        return diffs
    end

    function M.pending()
        return #order
    end

    -- A respawn invalidates anything captured against the old character.
    local sc = BX.scope("core.restore")
    ch.onSpawn(sc, "restore.respawn", function(char)
        local dropped = 0
        for i = #order, 1, -1 do
            local key = order[i]
            local e = entries[key]
            if e and e.char and e.char ~= char then
                entries[key] = nil
                table.remove(order, i)
                dropped = dropped + 1
            end
        end
        if dropped > 0 then
            log.trace("dropped %d entries captured against the old character", dropped)
        end
    end)

    return M
end)

--[[ ==== core/config.lua ============================================= ]]
-- Tunable constants. One table, one place to change behaviour.
--
-- Anything a human might want to adjust belongs here rather than as a literal
-- buried in a feature. Values carry the reasoning that produced them, because
-- several of these were measured against the live game and guessing at them
-- again would undo that work.
BX.module("core.config", function(BX)
    return {
        -- Travel. 500 studs/s is the measured ceiling the server accepts on a
        -- carry leg; above it the server voids the egg ("carry: 590 studs/s
        -- VOIDED by the server"). Do not raise without re-measuring.
        CARRY_SPEED        = 500,
        OUTBOUND_SPEED_MIN = 500,
        OUTBOUND_SPEED_MAX = 1200,

        -- Below this frame rate the client is weak (phones, old laptops) and
        -- the expensive passes thin themselves out rather than stutter.
        LITE_FPS           = 25,

        -- Stats/FPS counter refresh. Four times a second reads as live without
        -- costing a per-frame string build.
        STATS_HZ           = 4,

        -- Logging verbosity at startup: TRACE=1 INFO=2 WARN=3 ERROR=4
        LOG_LEVEL          = 2,

        -- The menu's background image, applied at startup without anyone
        -- typing it in. A saved profile with its own background overrides it;
        -- Clear Background in Config removes it for the session. Empty string
        -- for no default.
        DEFAULT_BACKGROUND = "108858454360177",
    }
end)

--[[ ==== core/state.lua ============================================== ]]
-- Shared mutable state.
--
-- Deliberately small and deliberately explicit. The old build let features
-- reach into each other's upvalues, which is exactly why a change in one
-- corner broke something unrelated. Anything crossing a module boundary is
-- declared here, with a comment saying who writes it.
BX.module("core.state", function(BX)
    return {
        heldEggUid   = nil,    -- written by: features.autosteal
        autoStealOn  = false,  -- written by: ui.window toggle
        -- written by: features.farm.treadmill_on, read by: features.treadmill.
        -- While true the Anti Treadmill poll stands down instead of doffing the
        -- player the Farm tab just deliberately parked on the belt.
        stayOnTreadmill = false,
        lastFps      = 0,      -- written by: ui.stats
        startedAt    = os.clock(),
    }
end)

--[[ ==== core/util.lua =============================================== ]]
-- Small helpers with no dependencies of their own.
BX.module("core.util", function(BX)
    local M = {}

    function M.clamp(v, lo, hi)
        return math.max(lo, math.min(hi, v))
    end

    function M.round(v, places)
        local m = 10 ^ (places or 0)
        return math.floor(v * m + 0.5) / m
    end

    -- Wait that respects the generation guard: returns false the moment this
    -- copy of the hub is retired, so callers can bail out of a loop.
    function M.wait(seconds)
        task.wait(seconds)
        return BX.alive()
    end

    -- Format a count the way the stats panel wants it (1234 -> "1.2k").
    function M.short(n)
        if n >= 1e6 then return ("%.1fM"):format(n / 1e6) end
        if n >= 1e3 then return ("%.1fk"):format(n / 1e3) end
        return tostring(math.floor(n))
    end

    return M
end)

--[[ ==== features/treadmill.lua ====================================== ]]
-- =============================================================================
-- FEATURES.TREADMILL: stay off the belt
-- =============================================================================
--
--     local tm = BX.require("features.treadmill")
--     tm.setEnabled(true)    -- default; V3.1 BX.autoDoff = true
--     tm.isOn()
--
-- WHAT IT PROTECTS AGAINST
--
-- TreadmillBottom, a BasePart inside your OWN plot folder (resolved through
-- PlotState.ResolvePlot().PlotFolder). Standing on it mounts you to the belt,
-- and a mounted character cannot be moved by us - which is what breaks a run
-- that passes over the plot on its way out to an egg.
--
-- IT CHANGES NOTHING LOCALLY, WHICH IS WHY THERE IS NOTHING TO RESTORE
--
-- V3.1 does not fight the belt, anchor through it or zero velocity. It asks the
-- SERVER to dismount, once, through the game own remote:
--
--     RF/Treadmill/AskDoff
--
-- So toggling off, dying, re-executing or unloading the UI needs no undo step -
-- there is no modified state to put back. Stopping the loop is the whole of it.
--
-- IT IS A POLL, NOT A PER-FRAME SCAN
--
-- V3.1 K.TREADMILL_POLL is 1.5 seconds, and the check itself is one cached part
-- reference plus a CFrame:PointToObjectSpace - not a descendants walk and not a
-- raycast. After a successful doff it waits 2s before asking again, so the
-- server is never asked twice while it is already acting.
--
-- The part is cached here rather than re-resolved every poll: it does not move,
-- and PlotState.ResolvePlot plus a recursive FindFirstChild every 1.5s for a
-- whole session is a cost with nothing to show for it. The cache is dropped on
-- respawn, because a rejoin or a plot change gives a different folder.

BX.module("features.treadmill", function(BX)
    local svc = BX.require("core.services")
    local data = BX.require("core.data")
    local ch  = BX.require("core.character")
    local dev = BX.require("core.device")
    local net = BX.require("core.net")
    local st  = BX.require("core.state")
    local log = BX.require("boot.log").for_module("treadmill")

    local M = {}

    local K = {
        PAD       = 6,     -- V3.1 K.TREADMILL_PAD - studs of slack on X/Z
        Y_SLACK   = 12,
        POLL      = 1.5,   -- V3.1 K.TREADMILL_POLL
        AFTER_OFF = 2.0,   -- do not re-ask while the server is acting on it
        PART_TTL  = 30,    -- how long a resolved part reference stays good
    }
    M.K = K

    -- THROUGH core.data. A private FindFirstChild + require here bypassed
    -- the one place that knows whether this executor can require game
    -- modules at all, and cached a miss forever. core.data probes once,
    -- lets a miss expire, and backs off - see core/data.lua.
    local PlotState = data.plotState()

    -- Moved to core.net, exactly as the note here said it would be once a second
    -- feature needed it: Farm's treadmill hold and Equip Best Pets both do.
    local netCall = net.call
    M.netCall = netCall

    local partCache, partAt = nil, 0

    local function treadmillPart()
        local now = os.clock()
        if partCache and partCache.Parent and (now - partAt) < K.PART_TTL then
            return partCache
        end
        local found = nil
        BX.try("treadmill.resolvePart", function()
            local plot = PlotState and PlotState.ResolvePlot and PlotState.ResolvePlot()
            if type(plot) ~= "table" or not plot.PlotFolder then return end
            local p = plot.PlotFolder:FindFirstChild("TreadmillBottom", true)
            if p and p:IsA("BasePart") then found = p end
        end)
        partCache, partAt = found, now
        return found
    end

    -- Object-space bounds test with a pad. Cheap on purpose.
    function M.onBelt()
        local part = treadmillPart()
        local hrp = ch.root()
        if not part or not hrp then return false end
        local rel = part.CFrame:PointToObjectSpace(hrp.Position)
        local half = part.Size * 0.5
        return math.abs(rel.X) <= half.X + K.PAD
           and math.abs(rel.Z) <= half.Z + K.PAD
           and math.abs(rel.Y) <= K.Y_SLACK
    end

    local enabled = true     -- V3.1 default: BX.autoDoff = true
    local sc = nil
    local stats = { checks = 0, caught = 0, doffed = 0, refused = 0, yielded = 0 }
    function M.stats() return table.clone(stats) end
    function M.isOn() return enabled end

    local function step()
        if not enabled then return end

        -- TWO FEATURES, OPPOSITE INTENTIONS, ONE EXPLICIT OWNER.
        --
        -- Anti Treadmill (Main) keeps a run from being caught by the belt.
        -- Stay On Treadmill (Farm) deliberately parks the player on it to farm
        -- AFK. Left to themselves they would fight every poll: the hold puts you
        -- on, this asks the server to take you off, forever.
        --
        -- So the belt has one owner at a time and the FARM HOLD WINS while it is
        -- on, because it is the thing the user just switched on deliberately.
        -- state.stayOnTreadmill is written only by features.farm.treadmill_on
        -- and read only here.
        if st.stayOnTreadmill then
            stats.yielded = stats.yielded + 1
            return
        end

        stats.checks = stats.checks + 1
        if not M.onBelt() then return end

        stats.caught = stats.caught + 1
        local ok, msg = netCall("RF/Treadmill/AskDoff")
        if ok == true then
            stats.doffed = stats.doffed + 1
            log.info("standing on the belt - AskDoff accepted")
        else
            stats.refused = stats.refused + 1
            log.warn("standing on the belt - AskDoff refused: %s %s",
                tostring(ok), tostring(msg or ""))
        end
        -- The server is acting on it; asking again immediately only queues
        -- another call it will answer the same way.
        task.wait(dev.scale(K.AFTER_OFF))
    end

    function M.arm()
        if sc then return true end
        sc = BX.scope("features.treadmill")

        -- A poll, on a scope, so toggling off / re-executing / unloading takes
        -- it with them. dev.scale so a weak client polls less often rather than
        -- spending frames it does not have.
        sc:loop("watch", dev.scale(K.POLL), step)

        -- A respawn can mean a different plot folder.
        ch.onSpawn(sc, "treadmill.respawn", function()
            partCache, partAt = nil, 0
        end)

        log.info("armed (poll %.1fs, %s)", dev.scale(K.POLL),
            enabled and "enabled" or "disabled")
        return true
    end

    function M.disarm()
        if not sc then return end
        sc:destroy()
        sc = nil
        partCache, partAt = nil, 0
        -- Nothing to restore: this never modified local state. See the header.
        log.info("disarmed (%d checks, %d caught, %d doffed)",
            stats.checks, stats.caught, stats.doffed)
    end

    -- The toggle only flips the flag; the watcher stays armed so switching it
    -- back on does not need to rebuild anything.
    function M.setEnabled(on)
        enabled = on and true or false
        log.info("anti treadmill %s", enabled and "ON" or "OFF")
        if enabled then M.arm() end
    end

    return M
end)

--[[ ==== features/esp/cards.lua ====================================== ]]
-- =============================================================================
-- FEATURES.ESP.CARDS: V3.1's ESP card, in the BlyxoHub tag's visual language
-- =============================================================================
--
--     local h = cards.open("eggs")
--     h:show(i, { pos=, title=, sub=, accent=, icon=, lines=, scale=, target= })
--     h:shown(n)     -- everything past slot n is hidden
--     h:close()
--
-- THE STRUCTURE IS V3.1'S, TRACED, NOT REDESIGNED.
--
--   anchor Part 0.2 studs, anchored, CanCollide/CanQuery/CanTouch/CastShadow off
--   BillboardGui  AlwaysOnTop, LightInfluence 0, MaxDistance 1e6, Active false,
--                 Adornee = anchor, PARENT = anchor (not a ScreenGui: a
--                 billboard in a protected container never renders)
--   frame 190x40, UICorner 8, ClipsDescendants, one UIScale
--   accent  2px wide, (1,-8) tall, at (3,4)     <- the rarity colour
--   icon    24x24 at (9,8)                      <- the pet's icon
--   title   (38,3)  (1,-44)x15  GothamBold 12
--   sub     (38,18) (1,-44)x20  Gotham 10, RichText
--
-- A POOL INDEXED BY SLOT, exactly as V3.1 does it: "built once, then reused for
-- whatever egg lands in this slot". Cards are never keyed by uid, so a field
-- that churns uids does not churn Instances.
--
-- AND CONSTRUCTION NEVER HAPPENS ON THE CALLER'S FRAME.
--
-- A card is 13 Instances and costs about 0.78ms to build. The first Egg ESP pass
-- wants 32 of them, and because that pass ran inside the toggle's own callback
-- the switch cost 19-42ms and dropped a frame EVERY time it went on - measured
-- on the live client. show() now queues a slot it has no card for, and the frame
-- handler builds K.BUILD_PER_FRAME of them per frame, so the full set is up in
-- about a fifth of a second and no frame pays for more than three.
--
-- The consequence to remember: the pool can briefly have HOLES, so nothing here
-- may use `#pool`. `pool.n` counts what exists and `pool.high` bounds the walk.
--
-- THE LOOK IS THE BLYXOHUB TAG'S.
--
-- Traced from V3.1's player tag: a vertical UIGradient from bgTop to bgBottom,
-- a UIStroke in Border mode carrying its OWN gradient from accent to element,
-- GothamBold for the name and Gotham for the detail. The tag is a pill because
-- it holds one word; a card holds three lines, so it keeps the tag's gradient,
-- stroke and type and takes the card's radius. They read as one hub.
--
-- ONE RenderStepped FOR EVERY CARD OF EVERY FEATURE, throttled to K.VIS_HZ,
-- doing exactly what V3.1's espVis does:
--
--   * enable/disable by distance, written only on change
--   * size follows distance through BlyxoEspScale, applied to the UIScale AND
--     the billboard together so the card grows as a whole
--   * opacity fades over the last K.FADE_BAND studs so edge cards dissolve
--     instead of popping
--   * every write guarded by a changed-by-more-than check
--
-- Nothing here allocates per frame: no tables are built in the loop.

BX.module("features.esp.cards", function(BX)
    local svc = BX.require("core.services")
    local dev = BX.require("core.device")
    local log = BX.require("boot.log").for_module("esp.cards")

    local M = {}

    local K = {
        W = 190, H = 40,
        VIS_HZ = 12,          -- V3.1 K.ESP_VIS_HZ cadence for the size follow
        MAX_DIST = 2200,      -- V3.1 K.ESP_MAX_DIST
        FADE_BAND = 260,      -- V3.1 K.ESP_FADE_BAND
        BASE_ALPHA = 0.42,    -- V3.1 frame.BackgroundTransparency
        BASE_STROKE = 0.55,
        -- CARDS PER FRAME, NOT CARDS PER PASS.
        --
        -- A card is 13 Instances. Measured on the live client: building one
        -- costs ~0.78ms, so the 32 cards the first Egg ESP pass asks for were
        -- 416 Instances and 19-42ms of work inside the toggle's own callback -
        -- one dropped frame every single time the switch went on. The pass now
        -- QUEUES what it wants and the frame handler builds a few per frame, so
        -- the cards still appear in about a fifth of a second and no single
        -- frame pays for more than three of them.
        BUILD_PER_FRAME = 3,
    }
    M.K = K

    -- The tag's palette, from V3.1 makeTag.
    local C = {
        bgTop   = Color3.fromRGB(26, 26, 30),
        bgBot   = Color3.fromRGB(14, 14, 17),
        accent  = Color3.fromRGB(206, 206, 212),
        element = Color3.fromRGB(41, 41, 48),
        title   = Color3.fromRGB(246, 242, 234),
        sub     = Color3.fromRGB(168, 158, 144),
    }

    -- THE ESP CARD'S TYPE AND INLINE PALETTE, shared by every card feature so
    -- Egg ESP and Plot ESP read as one hub. One constant table, handed to
    -- show() as `style`, applied once per card (compared by identity).
    --
    --     Pet Name                          GothamBold 13, the tag's ivory
    --     12.5M/s  ·  Divine  ·  8.42kg     Gotham 10: green, rarity, neutral
    --     Mutation                          amber
    --
    -- Colours are the hub's own: the splash's ONLINE green for income and
    -- READY, its WHITE for the weight, the stats overlay's WARN amber for a
    -- mutation, the splash's GREY for a countdown. The rarity keeps the
    -- game's colour for it.
    M.STYLE = {
        titleFont = Enum.Font.GothamBold, titleSize = 13,
        subFont   = Enum.Font.Gotham,     subSize   = 10,
    }
    M.COL = {
        income = "57F287", neutral = "F0F0F6", mutation = "F0BE5A",
        dim = "8A8A92", ready = "57F287",
    }
    M.SEP = "  \u{B7}  "

    function M.tint(col, text)
        return ('<font color="#%s">%s</font>'):format(col, text)
    end

    function M.hex(c)
        return ("%02X%02X%02X"):format(
            math.floor(c.R * 255 + 0.5), math.floor(c.G * 255 + 0.5),
            math.floor(c.B * 255 + 0.5))
    end

    -- V3.1 BlyxoEspScale, unchanged.
    local function scaleFor(dist)
        return math.clamp(1.25 - (tonumber(dist) or 0) / 800, 0.6, 1.25)
    end

    local sc, folder, handles = nil, nil, 0
    local pools = {}      -- handle name -> { cards }

    -- FORWARD-DECLARED, because the frame handler inside ensure() drains the
    -- build queue and therefore calls both - and a `local function` declared
    -- further down is not in scope up here, it compiles to a nil global.
    local build, apply

    local function ensure()
        if sc then return end
        sc = BX.scope("features.esp.cards")
        folder = Instance.new("Folder")
        folder.Name = "BlyxoESP"
        sc:own(folder)
        folder.Parent = workspace

        local acc, step = 0, 1 / K.VIS_HZ
        sc:onFrame("vis", svc.RunService.RenderStepped, function(dt)
            -- DRAIN THE BUILD QUEUE FIRST, AND EVERY FRAME.
            --
            -- Above the throttle on purpose: the visibility pass only needs to
            -- run at K.VIS_HZ, but a queued card should appear as soon as the
            -- budget allows rather than waiting out a twelfth of a second. A few
            -- per frame is what keeps the enable off the caller's frame - see
            -- K.BUILD_PER_FRAME.
            local budget = dev.budget(K.BUILD_PER_FRAME)
            for _, pool in pairs(pools) do
                if budget <= 0 then break end
                for i, d in pairs(pool.pending) do
                    if budget <= 0 then break end
                    local c = build()
                    pool[i] = c
                    pool.n = pool.n + 1
                    if i > pool.high then pool.high = i end
                    apply(c, d)
                    pool.pending[i] = nil
                    budget = budget - 1
                end
            end

            acc = acc + (dt or 0)
            if acc < step then return end
            acc = 0
            local cam = workspace.CurrentCamera
            if not cam then return end
            local eye = cam.CFrame.Position

            for _, pool in pairs(pools) do
                for i = 1, pool.shown do
                    local c = pool[i]
                    if c and c.anchor.Parent then
                        local d = (c.pos - eye).Magnitude
                        local show = d <= K.MAX_DIST
                        if c.bb.Enabled ~= show then c.bb.Enabled = show end
                        if show then
                            local s = scaleFor(d)
                            if math.abs(c.lastScale - s) > 0.01 or c.lastH ~= c.baseH then
                                c.lastScale, c.lastH = s, c.baseH
                                c.scale.Scale = s
                                c.bb.Size = UDim2.fromOffset(K.W * s, c.baseH * s)
                            end
                            local fade = math.clamp((K.MAX_DIST - d) / K.FADE_BAND, 0, 1)
                            if math.abs(c.lastFade - fade) > 0.02 then
                                c.lastFade = fade
                                c.frame.BackgroundTransparency = 1 - (1 - K.BASE_ALPHA) * fade
                                c.title.TextTransparency = 1 - fade
                                c.sub.TextTransparency = 1 - fade
                                c.icon.ImageTransparency = 1 - fade
                                c.stroke.Transparency = 1 - (1 - K.BASE_STROKE) * fade
                            end
                        end
                    end
                end
            end
        end)
    end

    function build()
        local anchor = Instance.new("Part")
        anchor.Name = "EggAnchor"
        anchor.Anchored = true
        anchor.CanCollide = false
        anchor.CanQuery = false
        anchor.CanTouch = false
        anchor.CastShadow = false
        anchor.Transparency = 1
        anchor.Size = Vector3.new(0.2, 0.2, 0.2)
        anchor.Parent = folder

        local bb = Instance.new("BillboardGui")
        bb.Name = "EggCard"
        bb.AlwaysOnTop = true
        bb.LightInfluence = 0
        bb.MaxDistance = 1e6          -- not math.huge: some clients reject inf
        bb.Size = UDim2.fromOffset(K.W, K.H)
        bb.StudsOffset = Vector3.new(0, 3, 0)
        bb.Active = false
        bb.Adornee = anchor
        bb.Enabled = false
        bb.Parent = anchor

        local frame = Instance.new("Frame")
        frame.Size = UDim2.fromOffset(K.W, K.H)
        frame.BackgroundColor3 = Color3.new(1, 1, 1)
        frame.BackgroundTransparency = K.BASE_ALPHA
        frame.BorderSizePixel = 0
        frame.ClipsDescendants = true
        frame.Parent = bb
        Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 8)

        -- The tag's vertical gradient.
        local grad = Instance.new("UIGradient", frame)
        grad.Color = ColorSequence.new(C.bgTop, C.bgBot)
        grad.Rotation = 90

        local scaleObj = Instance.new("UIScale")
        scaleObj.Scale = 1
        scaleObj.Parent = frame

        -- The tag's bordered stroke, with its own gradient.
        local stroke = Instance.new("UIStroke", frame)
        stroke.Color = Color3.new(1, 1, 1)
        stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
        stroke.Thickness = 1
        stroke.Transparency = K.BASE_STROKE
        local sg = Instance.new("UIGradient", stroke)
        sg.Color = ColorSequence.new(C.accent, C.element)
        sg.Rotation = 90

        local accent = Instance.new("Frame")
        accent.Name = "Accent"
        accent.Position = UDim2.fromOffset(3, 4)
        accent.Size = UDim2.new(0, 2, 1, -8)
        accent.BorderSizePixel = 0
        accent.BackgroundColor3 = Color3.fromRGB(194, 142, 54)
        accent.Parent = frame
        Instance.new("UICorner", accent).CornerRadius = UDim.new(1, 0)

        local icon = Instance.new("ImageLabel")
        icon.Name = "Icon"
        icon.Position = UDim2.fromOffset(9, 8)
        icon.Size = UDim2.fromOffset(24, 24)
        icon.BackgroundTransparency = 1
        icon.ScaleType = Enum.ScaleType.Fit
        icon.Image = ""
        icon.Parent = frame

        local title = Instance.new("TextLabel")
        title.Name = "Title"
        title.Position = UDim2.fromOffset(38, 3)
        title.Size = UDim2.new(1, -44, 0, 15)
        title.BackgroundTransparency = 1
        title.Font = Enum.Font.GothamBold
        title.TextSize = 12
        title.TextColor3 = C.title
        title.TextXAlignment = Enum.TextXAlignment.Left
        title.TextTruncate = Enum.TextTruncate.AtEnd
        title.Text = ""
        title.Parent = frame

        local sub = Instance.new("TextLabel")
        sub.Name = "Sub"
        sub.Position = UDim2.fromOffset(38, 18)
        sub.Size = UDim2.new(1, -44, 0, 20)
        sub.BackgroundTransparency = 1
        sub.Font = Enum.Font.Gotham
        sub.TextSize = 10
        sub.TextColor3 = C.sub
        sub.TextXAlignment = Enum.TextXAlignment.Left
        sub.TextYAlignment = Enum.TextYAlignment.Top
        sub.RichText = true          -- the rarity is coloured inline, as in V3.1
        sub.Text = ""
        sub.Parent = frame

        return {
            anchor = anchor, bb = bb, frame = frame, stroke = stroke,
            accent = accent, icon = icon, title = title, sub = sub,
            scale = scaleObj, pos = Vector3.zero, baseH = K.H,
            lastScale = -1, lastFade = -1, lastH = -1,
            lastTitle = nil, lastSub = nil, lastIcon = nil, lastStyle = nil,
        }
    end

    -- Writes one card's fields. Every write is guarded, so an unchanged card
    -- costs nothing. Shared by the live path and the deferred builder, so a card
    -- that was queued last frame is filled by exactly the same code.
    function apply(c, d)
        if c.pos ~= d.pos then
            c.pos = d.pos
            c.anchor.CFrame = CFrame.new(d.pos)
        end

        -- Mutations get their own line and the card grows to fit, as in V3.1.
        local h = (d.lines and d.lines > 1) and (K.H + 12) or K.H
        if c.baseH ~= h then
            c.baseH = h
            c.frame.Size = UDim2.fromOffset(K.W, h)
            c.sub.Size = UDim2.new(1, -44, 0, h - 20)
        end

        local titleText = (d.target and "\u{25B8} " or "") .. tostring(d.title or "")
        if titleText ~= c.lastTitle then
            c.lastTitle = titleText
            c.title.Text = titleText
        end
        if d.sub ~= c.lastSub then
            c.lastSub = d.sub
            c.sub.Text = tostring(d.sub or "")
        end
        if d.icon ~= c.lastIcon then
            c.lastIcon = d.icon
            c.icon.Image = tostring(d.icon or "")
        end
        if d.accent then c.accent.BackgroundColor3 = d.accent end

        -- OPTIONAL TYPE OVERRIDE, per feature. A pool belongs to one feature,
        -- so a card only ever sees its owner's style; a feature that passes
        -- none keeps the V3.1 defaults built above. Compared by identity: the
        -- caller hands in one constant table, so this write happens once per
        -- card, not per pass.
        local st = d.style
        if st ~= c.lastStyle then
            c.lastStyle = st
            c.title.Font = (st and st.titleFont) or Enum.Font.GothamBold
            c.title.TextSize = (st and st.titleSize) or 12
            c.sub.Font = (st and st.subFont) or Enum.Font.Gotham
            c.sub.TextSize = (st and st.subSize) or 10
        end
    end

    local Handle = {}
    Handle.__index = Handle

    -- Fills slot `i`, or QUEUES it when that slot has no card yet.
    --
    -- THE CALLER NEVER PAYS FOR CONSTRUCTION. Building a card is 13 Instances,
    -- and a first pass asking for 32 of them inside a toggle callback is the
    -- 19-42ms hitch that was measured every time Egg ESP went on. The queue is
    -- drained a few cards per frame by the handler in ensure(), so the cost is
    -- spread over a fifth of a second instead of landing in one frame.
    function Handle:show(i, d)
        local pool = pools[self.name]
        local c = pool[i]
        if not c then
            pool.pending[i] = d
            return
        end
        apply(c, d)
    end

    -- Everything past slot n is hidden, not destroyed: the pool is the point.
    --
    -- Bounded by `high` rather than `#pool`: deferred construction means the
    -- pool can briefly have holes, and `#` on a table with holes is undefined -
    -- it would leave a card past the cut still drawn.
    function Handle:shown(n)
        local pool = pools[self.name]
        pool.shown = n
        for i = n + 1, pool.high do
            local c = pool[i]
            if c and c.bb.Enabled then c.bb.Enabled = false end
        end
        -- A slot past the cut is no longer wanted: drop its queued request
        -- rather than building a card that is about to be hidden.
        for i in pairs(pool.pending) do
            if i > n then pool.pending[i] = nil end
        end
    end

    function Handle:count()
        local pool = pools[self.name]
        return pool.n, pool.shown
    end

    function Handle:close()
        local pool = pools[self.name]
        for i = 1, pool.high do
            local c = pool[i]
            if c then pcall(function() c.anchor:Destroy() end) end
        end
        pools[self.name] = nil
        handles = handles - 1
        if handles <= 0 then
            handles = 0
            if sc then sc:destroy() sc = nil end
            folder, pools = nil, {}
            log.info("released")
        end
    end

    function M.open(name)
        ensure()
        handles = handles + 1
        pools[name] = { shown = 0, pending = {}, n = 0, high = 0 }
        return setmetatable({ name = name }, Handle)
    end

    function M.liveCount()
        local n = 0
        for _, pool in pairs(pools) do n = n + pool.n end
        return n
    end

    -- How many cards are still queued, across every pool. Watched so a queue
    -- that stops draining is visible in the health line rather than showing up
    -- as "the ESP only draws half the eggs".
    function M.pendingCount()
        local n = 0
        for _, pool in pairs(pools) do
            for _ in pairs(pool.pending) do n = n + 1 end
        end
        return n
    end

    BX.profile.watch("esp.cards", M.liveCount)
    BX.profile.watch("esp.cards.queued", M.pendingCount)

    return M
end)

--[[ ==== features/esp/eggs.lua ======================================= ]]
-- =============================================================================
-- FEATURES.ESP.EGGS: V3.1's Egg ESP, same information, cleaner internals
-- =============================================================================
--
--     BX.require("features.esp.eggs").setEnabled(true)
--
-- THE INFORMATION IS V3.1'S, TRACED FROM BX.espEggs. Not redesigned:
--
--     title  the PET's name (Data.Assets DisplayName for the AssetCategory),
--            prefixed with a marker when it is the Auto Steal target
--     sub    income/s  .  rarity in its own colour  .  weight
--            and mutations on their OWN line, the card growing to fit
--     accent the rarity's Color3, down the left edge
--     icon   the pet's icon
--
-- V3.1's own note on the order: "SUB: rate, then the rarity in its own colour,
-- then weight. The rarity is the thing your eye should catch." Income per second
-- is first because it is the main reason to have Egg ESP on at all - an earlier
-- rewrite of mine dropped it entirely, which removed the point of the feature.
--
-- WHAT CHANGED IS ONLY THE MACHINERY:
--
--   * the egg list comes from features.eggs' cache - no field read, no
--     GetDescendants, no Workspace walk, ever
--   * one pooled card per slot, reused for whatever egg lands in it
--   * one shared throttled RenderStepped for every card (features.esp.cards)
--   * no per-egg connection, and the update reuses one scratch table
--   * text and icon written only when the value changed
--   * OFF destroys every card and the folder immediately
--   * no flag on the toggle, so it never switches itself on at startup
--
-- ABOVE THE EGG, NOT INSIDE IT.
--
-- The record's BoundsCFrame is the egg's CENTRE, so a fixed offset draws the card
-- inside anything large. AssetScale is the per-egg size roll the game applies, so
-- the anchor is lifted by a base clearance plus a scale-proportional term: a big
-- egg gets its card above its top, a small one does not float.

BX.module("features.esp.eggs", function(BX)
    local dev   = BX.require("core.device")
    local eggs  = BX.require("features.eggs")
    local data  = BX.require("core.data")
    local cards = BX.require("features.esp.cards")
    local log   = BX.require("boot.log").for_module("esp.eggs")

    local M = {}

    local K = {
        REFRESH = 0.5,        -- V3.1 K.ESP_REFRESH: the data pass
        MAX_CARDS = 40,       -- a bounded pool, as V3.1 had
        LIFT_BASE = 2.2,      -- studs of clearance above the egg's centre
        LIFT_SCALE = 3.4,     -- times AssetScale, so big eggs clear their top
    }
    M.K = K

    local sc, handle, enabled = nil, nil, false
    local stats = { updates = 0, listed = 0, shown = 0 }
    function M.stats() return table.clone(stats) end
    function M.isOn() return enabled end

    -- The shared ESP type and palette (features/esp/cards.lua), so this card
    -- and Plot ESP's read as one hub.
    local STYLE, COL, SEP, tint, hex = cards.STYLE, cards.COL, cards.SEP, cards.tint, cards.hex

    -- V3.1's rate format: 6.78M, 15.8M, 1.2K.
    local function rate(n)
        n = tonumber(n) or 0
        for _, u in ipairs({ { 1e12, "T" }, { 1e9, "B" }, { 1e6, "M" }, { 1e3, "K" } }) do
            if n >= u[1] then
                local v = n / u[1]
                local txt = (v < 10) and ("%.2f"):format(v) or ("%.1f"):format(v)
                return (txt:gsub("%.?0+$", "")) .. u[2]
            end
        end
        return tostring(math.floor(n))
    end

    -- Reused every pass: the update allocates no new table.
    local scratch = {}

    local function update()
        if not enabled or not handle then return end
        stats.updates = stats.updates + 1

        local cam = workspace.CurrentCamera
        local list = eggs.list()
        if not cam or not list then return end
        local dir = data.assetsDir()
        local eye = cam.CFrame.Position

        for i = #scratch, 1, -1 do scratch[i] = nil end
        for _, e in ipairs(list) do
            if e.pos and (e.pos - eye).Magnitude <= cards.K.MAX_DIST then
                scratch[#scratch + 1] = e
            end
        end
        stats.listed = #scratch
        table.sort(scratch, function(a, b) return (a.value or 0) > (b.value or 0) end)

        local n = math.min(#scratch, K.MAX_CARDS)
        for i = 1, n do
            local e = scratch[i]
            local d = e.assetCategory and dir and dir[e.assetCategory] or nil

            local colour = Color3.fromRGB(200, 200, 200)
            local rarityName = (e.rarity and e.rarity ~= "?") and e.rarity or nil
            if d and d.Rarity then
                if typeof(d.Rarity.Color) == "Color3" then colour = d.Rarity.Color end
                rarityName = rarityName or d.Rarity.DisplayName or d.Rarity._id
            end

            -- rate . rarity (coloured) . weight  -- V3.1's order exactly,
            -- each piece in its own colour so only the number is green.
            local bits = { tint(COL.income, "<b>" .. rate(e.value or 0) .. "/s</b>") }
            if rarityName then
                bits[#bits + 1] = tint(hex(colour), rarityName)
            end
            local kg = tonumber(e.kg) or 0
            if kg > 0 then
                bits[#bits + 1] = tint(COL.neutral,
                    kg >= 100 and ("%.0fkg"):format(kg) or ("%.1fkg"):format(kg))
            end
            local sub = table.concat(bits, SEP)

            -- Mutations get their own line, in the mutation amber, and the
            -- card grows to fit.
            local lines = 1
            if type(e.mutations) == "table" and #e.mutations > 0 then
                local names = {}
                for _, mu in ipairs(e.mutations) do
                    names[#names + 1] = tostring(type(mu) == "table"
                        and (mu.DisplayName or mu._id or "?") or mu)
                end
                sub = sub .. "\n" .. tint(COL.mutation, table.concat(names, " \u{B7} "))
                lines = 2
            end

            local lift = K.LIFT_BASE + (tonumber(e.assetScale) or 1) * K.LIFT_SCALE
            handle:show(i, {
                pos = e.pos + Vector3.new(0, lift, 0),
                title = e.name,
                sub = sub,
                accent = colour,
                icon = d and d.Icon or nil,
                lines = lines,
                target = e.isTarget,
                style = STYLE,
            })
        end
        handle:shown(n)
        stats.shown = n
    end

    function M.setEnabled(on)
        on = on and true or false
        if on == enabled then return true end
        enabled = on

        if not on then
            if handle then handle:close() handle = nil end
            if sc then sc:destroy() sc = nil end
            log.info("off")
            return true
        end

        handle = cards.open("eggs")
        sc = BX.scope("features.esp.eggs")
        sc:loop("update", dev.scale(K.REFRESH), update)
        log.info("on (max %d cards, %.2fs, range %d)",
            K.MAX_CARDS, dev.scale(K.REFRESH), cards.K.MAX_DIST)
        return true
    end

    return M
end)

--[[ ==== features/esp/plot.lua ======================================= ]]
-- =============================================================================
-- FEATURES.ESP.PLOT: what is hatching on my plot
-- =============================================================================
--
--     BX.require("features.esp.plot").setEnabled(true)
--
-- WHAT IT SHOWS, traced from V3.1's plot ESP: the PET that will hatch - not the
-- egg shell - its rarity, what it will be worth, any mutations, and how long is
-- left before it is ready (or READY). Laid out as
--
--     Pet Name
--     12.5M/s  ·  Divine  ·  8.42kg        income green, rarity its colour
--     Mutation  ·  5m 30s                  amber, then the state, dim
--
-- on the same card renderer Egg ESP uses (features/esp/cards.lua), with the
-- plot's own type sizes passed as a style; Egg ESP's cards are not changed.
--
-- WHERE THE EGGS ACTUALLY ARE. THIS IS WHAT THE LAST VERSION GOT WRONG.
--
-- V3.1 reads workspace.PlacedEggRenders, whose children are Models named
--
--     <ownerUserId>_<eggUid>
--
-- and pairs each one with EggState.ReadOwnerEggs(myUserId)[uid]. The previous
-- rewrite looked for a "Uid" attribute on descendants of Workspace.Plots.<slot>
-- instead, which is why it reported eggs=0 across 349 updates: it was reading
-- the wrong place entirely.
--
-- ONLY OUR PLOT, BY CONSTRUCTION. The name prefix IS our own UserId, so another
-- player's placed eggs cannot match. Nothing scans other plots.
--
-- THE COUNTDOWN, exactly as V3.1 computes it:
--
--     readyAt = Placement.PlacedAt + Egg.GrowthTime / GrowthSpeedMultiplier
--
-- and EggState.IsReadyToHatch(uid) is the authority on "ready". The remaining
-- time is re-rendered on the data pass, not per frame - a countdown that ticks
-- four times a second reads the same as one that ticks sixty times.
--
-- NO STALE REFERENCES: the render models are read fresh each pass and never
-- stored, and a card whose uid is no longer present is released immediately.

BX.module("features.esp.plot", function(BX)
    local svc   = BX.require("core.services")
    local dev   = BX.require("core.device")
    local ch    = BX.require("core.character")
    local data  = BX.require("core.data")
    local util  = BX.require("core.util")
    local eggs  = BX.require("features.eggs")
    local cards = BX.require("features.esp.cards")
    local log   = BX.require("boot.log").for_module("esp.plot")

    local M = {}

    local K = { RATE = 0.5, MAX_CARDS = 24 }
    M.K = K

    -- The shared ESP type and palette (features/esp/cards.lua), so this card
    -- and Egg ESP's read as one hub. Here the second line is
    --     Mutation  ·  5m 30s        amber, then the hatch state, dim
    -- and READY takes the green so it reads as the good news it is.
    local STYLE, COL, SEP, tint, hex = cards.STYLE, cards.COL, cards.SEP, cards.tint, cards.hex

    local sc, handle, enabled = nil, nil, false
    local stats = { updates = 0, eggs = 0, ready = 0 }
    function M.stats() return table.clone(stats) end
    function M.isOn() return enabled end

    -- "1h 05m" / "5m 30s" / "12s", V3.1 fmtLeft.
    local function timeLeft(seconds)
        seconds = math.max(0, math.floor(seconds))
        local h = math.floor(seconds / 3600)
        local m = math.floor(seconds / 60) % 60
        if h > 0 then return ("%dh %02dm"):format(h, m) end
        if m > 0 then return ("%dm %02ds"):format(m, seconds % 60) end
        return ("%ds"):format(seconds)
    end

    local function update()
        if not enabled or not handle then return end
        stats.updates = stats.updates + 1

        local rendered = workspace:FindFirstChild("PlacedEggRenders")
        if not rendered then
            handle:shown(0)
            stats.eggs = 0
            return
        end

        local ES = data.eggState()
        local dir = data.assetsDir()
        local me = svc.Players.LocalPlayer and svc.Players.LocalPlayer.UserId
        if not me then return end
        local prefix = tostring(me) .. "_"

        local recs = {}
        BX.try("esp.plot.readOwner", function()
            recs = (ES and ES.ReadOwnerEggs and ES.ReadOwnerEggs(me)) or {}
        end)

        local wanted, n, readyN = {}, 0, 0
        -- GetChildren on ONE folder, not GetDescendants on the world.
        for _, m in ipairs(rendered:GetChildren()) do
            if m:IsA("Model") and m.Name:sub(1, #prefix) == prefix then
                local uid = m.Name:sub(#prefix + 1)
                local pos
                BX.try("esp.plot.pivot", function() pos = m:GetPivot().Position end)
                if pos then
                    n = n + 1
                    wanted[uid] = true

                    local rec = recs[uid]
                    local d = rec and dir and dir[rec.AssetCategory] or nil
                    local title = (d and d.DisplayName ~= "" and d.DisplayName)
                        or (rec and tostring(rec.AssetCategory)) or "Egg"
                    local rarity = d and d.Rarity
                        and tostring(d.Rarity.DisplayName or d.Rarity._id or "") or ""
                    local colour = (d and d.Rarity and typeof(d.Rarity.Color) == "Color3")
                        and d.Rarity.Color or Color3.fromRGB(190, 190, 200)

                    -- Mutations, joined the way V3.1 joins them.
                    local muts = ""
                    if rec and type(rec.Mutations) == "table" and #rec.Mutations > 0 then
                        local names = {}
                        for _, mu in ipairs(rec.Mutations) do
                            names[#names + 1] = tostring(type(mu) == "table"
                                and (mu.DisplayName or mu._id or "?") or mu)
                        end
                        muts = table.concat(names, " \u{B7} ")
                    end

                    -- Ready, or how long is left.
                    local ready = false
                    BX.try("esp.plot.ready", function()
                        ready = (ES and ES.IsReadyToHatch and ES.IsReadyToHatch(uid)) == true
                    end)
                    local when = "growing"
                    if ready then
                        readyN = readyN + 1
                        when = "READY"
                    elseif rec then
                        local grow = d and d.Egg and tonumber(d.Egg.GrowthTime)
                        local placed = rec.Placement and tonumber(rec.Placement.PlacedAt)
                        local mult = math.max(tonumber(rec.GrowthSpeedMultiplier) or 1, 0.01)
                        if grow and placed then
                            when = timeLeft(placed + grow / mult - os.time())
                        end
                    end

                    -- INCOME/S, FROM EGG ESP's OWN RESOLVER.
                    --
                    -- This line used to be `state . rarity` and nothing else, so
                    -- a placed egg never showed what the pet inside it earns -
                    -- the one number the card exists to tell you.
                    --
                    -- AND IT COULD NOT SIMPLY CALL eggs.value(rec). An OWNED egg
                    -- record does not carry its own uid: ReadOwnerEggs returns a
                    -- map keyed BY uid, so rec.Uid is nil, and eggs.value caches
                    -- by rec.Uid - so it throws on the cache write:
                    --     eggs.value(rec) -> table index is nil
                    -- measured on every one of 70 owned records.
                    --
                    -- features/eggs.lua is an Auto Steal dependency and is NOT
                    -- touched. Instead this hands the resolver a record carrying
                    -- the uid THIS LOOP ALREADY HAS as its table key. Same
                    -- resolver, same AssetEarnings path, same formatRate - there
                    -- is no second income system.
                    local rate = nil
                    if rec then
                        BX.try("esp.plot.value", function()
                            rate = eggs.value({
                                Uid = uid,
                                AssetCategory = rec.AssetCategory,
                                AssetScale = rec.AssetScale,
                                Mutations = rec.Mutations,
                            })
                        end)
                    end

                    -- The number on the egg in game: the pet base weight
                    -- stretched by this egg own AssetScale. Same formula
                    -- features/eggs.lua weightOf uses, off the same Directory
                    -- entry already resolved above.
                    local kg = d and d.Egg and tonumber(d.Egg.WeightKg)
                    if kg then kg = kg * (tonumber(rec and rec.AssetScale) or 1) end
                    if kg and kg <= 0 then kg = nil end

                    -- LINE 1: income . rarity . weight - Egg ESP's order, each
                    -- piece in its own colour so only the number is green.
                    -- Anything unresolved is omitted rather than shown as zero.
                    local bits = {}
                    if rate and rate > 0 then
                        bits[#bits + 1] = tint(COL.income, "<b>" .. eggs.formatRate(rate) .. "/s</b>")
                    end
                    if rarity ~= "" then
                        bits[#bits + 1] = tint(hex(colour), rarity)
                    end
                    if kg then
                        bits[#bits + 1] = tint(COL.neutral, kg >= 100
                            and ("%.0fkg"):format(kg) or ("%.1fkg"):format(kg))
                    end
                    local line1 = table.concat(bits, SEP)

                    -- LINE 2: the mutation, if any, then the hatch state. Its
                    -- own line so line 1 never has to truncate to make room -
                    -- 190px holds "12.5M/s . Divine . 8.42kg" and not much
                    -- more. The card is always two lines for a plot egg.
                    local state = ready and tint(COL.ready, "<b>READY</b>") or tint(COL.dim, when)
                    local line2 = (muts ~= "") and (tint(COL.mutation, muts) .. SEP .. state) or state

                    local sub = line1 .. "\n" .. line2
                    local lines = 2

                    -- ABOVE the egg, lifted by its size roll: the same rule
                    -- Egg ESP uses, so a large egg clears its own top.
                    local lift = 2.2 + (tonumber(rec and rec.AssetScale) or 1) * 3.4
                    handle:show(n, {
                        pos = pos + Vector3.new(0, lift, 0),
                        title = title, sub = sub, accent = colour,
                        icon = d and d.Icon or nil, lines = lines,
                        target = ready, style = STYLE,
                    })
                end
            end
        end

        -- The pool hides everything past slot n; nothing is destroyed per
        -- pass, which is the whole point of a pool.
        handle:shown(n)
        stats.eggs, stats.ready = n, readyN
    end

    function M.setEnabled(on)
        on = on and true or false
        if on == enabled then return true end
        enabled = on

        if not on then
            if handle then handle:close() handle = nil end
            if sc then sc:destroy() sc = nil end
            log.info("off")
            return true
        end

        handle = cards.open("plot")
        sc = BX.scope("features.esp.plot")
        sc:loop("update", dev.scale(K.RATE), update)
        -- A rejoin or a respawn can change which renders are ours.
        ch.onSpawn(sc, "esp.plot.respawn", function()
            if handle then handle:shown(0) end
        end)
        log.info("on (%.2fs)", dev.scale(K.RATE))
        return true
    end

    return M
end)

--[[ ==== features/eggs.lua =========================================== ]]
-- =============================================================================
-- FEATURES.EGGS: what is out there, and which one to take
-- =============================================================================
--
--     local eggs = BX.require("features.eggs")
--
--     eggs.list()               -- every valid egg right now (cached)
--     eggs.best(filter)         -- the one to go for, or nil
--     eggs.get(uid)             -- a fresh record for one uid, or nil
--     eggs.invalidate(reason)   -- next read re-reads the field
--     eggs.markStolen(uid)      -- stop offering it while the server catches up
--     eggs.stats()
--
-- RESPONSIBILITY. It answers "what eggs exist and what are they worth". It does
-- not move, grab, carry or decide when to do any of those.
--
-- WHERE THE DATA COMES FROM, AND WHY IT IS NOT A MAP SCAN.
--
-- The field is read from EggState.ReadFieldEggs(), a replicated module table.
-- That is a table read, not a workspace walk - so "scan the whole map" is not
-- what this costs, and the caching below exists to avoid re-deriving VALUES,
-- not to avoid a scan. The Workspace fallback (AreaEggSlotsClient) is only for
-- a fresh join before EggState has synced, and it is rate-limited hard because
-- it IS a descendants walk.
--
-- THE FIELD ARRIVES IN TWO HALVES, AND ACTING ON THE FIRST ONE IS WRONG.
--
-- Your own Forest nests resolve the instant your profile loads; the other fifty
-- eggs are shared state that has to replicate, and ReadFieldEggs returns
-- whatever has arrived so far. V3.1's log caught twelve seconds of it:
--
--     [851.36] eggs: 5 stealable, best Bear 262/s
--              ... 25 passes, still 5 ...
--     [863.56] eggs: 55 stealable, best Snowy Owl 104544933/s
--
-- Auto Steal started in that window picks a 240/s Bear as "best" off a map that
-- is 9% loaded, and a Titan steal in it cannot work because Titan is not in the
-- list yet. So once the full field has been seen, a snapshot that collapses to
-- a handful is replication catching up, not the map emptying: the last good
-- list is kept and we look again shortly. That guard is carried over as-is.

BX.module("features.eggs", function(BX)
    local svc = BX.require("core.services")
    local dev = BX.require("core.device")
    local data = BX.require("core.data")
    local log = BX.require("boot.log").for_module("eggs")

    local M = {}

    local K = {
        CACHE_TTL       = 0.5,   -- how long a built list stays good (scaled by device)
        MIN_REBUILD     = 0.1,   -- floor between rebuilds, so a signal burst costs one
        RAW_TTL         = 0.25,  -- how long a raw snapshot stays good
        FALLBACK_TTL    = 5.0,   -- the Workspace walk: rarely, and never in a loop
        STOLEN_FOR      = 120,   -- keep a stolen uid suppressed this long
        UNREACHABLE_FOR = 45,    -- V3.1 K.UNREACHABLE_COOLDOWN
        PARTIAL_FLOOR   = 8,     -- <= this many records after a full field = replication lag
        FULL_FIELD_MIN  = 10,    -- > this many records means we have seen the real field
        VALUE_CACHE_MAX = 600,   -- hard ceiling; the field is ~55, so this is generous
    }
    M.K = K

    ---------- game modules ----------

    -- THROUGH core.data, NOT A BARE NAME SEARCH.
    --
    -- This used to do FindFirstChild("Assets", true) and check IsA("ModuleScript").
    -- There is a FOLDER called ReplicatedStorage.Assets as well as the
    -- ModuleScript at ReplicatedStorage.Data.Assets, the folder is found first,
    -- the IsA check fails, and AssetsDir was left nil - so every egg reported
    -- rarity="?" and kg=0 for the whole life of this rebuild. Measured on the
    -- live field before the fix. core/data.lua resolves the PATH and explains
    -- the trap in full.
    local EggState, AssetEarnings, AssetsDir
    BX.try("eggs.resolveModules", function()
        EggState = data.eggState()
        AssetEarnings = data.assetEarnings()
        -- Data.Assets.Directory. This is what turns an AssetCategory into the
        -- PET NAME the player recognises - the thing inside the egg, not the
        -- egg model. V3.1 getEggDisplayName reads exactly this.
        AssetsDir = data.assetsDir()
    end)
    M.ready = (EggState ~= nil)
    if not M.ready then
        log.error("EggState not found - is this Steal An Egg?")
    end

    ---------- caches ----------

    local rawSnap, rawSnapAt = nil, 0
    local dirty, dirtyReason = false, nil
    local list, listAt       = nil, 0
    local fallbackAt         = 0
    local sawFullField       = false
    local saidPartial        = false
    local stolen             = {}   -- uid -> os.clock() when we took it
    local unreachable        = {}   -- uid -> os.clock() when it beat us
    local valueCache         = {}   -- uid -> income/sec
    local valueCacheN        = 0

    local stats = {
        scans = 0, cacheHits = 0, partialHeld = 0, fallbacks = 0,
        signals = 0, dirtyRebuilds = 0,
        lastScanMs = 0, lastConsidered = 0, lastKept = 0,
    }

    -- Registered so the health line reports OUR growth rather than Roblox's.
    -- Any of these climbing steadily across a long session is a leak we own.
    BX.profile.watch("eggs.list", function() return list and #list or 0 end)
    BX.profile.watch("eggs.values", function() return valueCacheN end)
    BX.profile.watch("eggs.unreachable", function()
        local n = 0
        for _ in pairs(unreachable) do n = n + 1 end
        return n
    end)
    BX.profile.watch("eggs.stolen", function()
        local n = 0
        for _ in pairs(stolen) do n = n + 1 end
        return n
    end)

    function M.invalidate(reason)
        list, listAt = nil, 0
        rawSnap, rawSnapAt = nil, 0
        dirty = false
        if reason then log.trace("invalidated: %s", reason) end
    end

    -- MARK, DO NOT REBUILD.
    --
    -- These signals can fire in bursts - a field shift moves many eggs at once.
    -- Rebuilding on each one would turn event-driven invalidation into
    -- something more expensive than the polling it replaced. The flag says
    -- "the next read must not trust the cache"; MIN_REBUILD keeps a burst from
    -- costing more than one rebuild.
    function M.markDirty(reason)
        dirty = true
        dirtyReason = reason
        stats.signals = (stats.signals or 0) + 1
    end

    function M.markStolen(uid)
        if uid then stolen[tostring(uid)] = os.clock() end
    end

    -- SOMETHING ABOUT THIS EGG IS NOT WORKING RIGHT NOW.
    --
    -- V3.1 marks a target unreachable when the grab finally fails and skips it
    -- for UNREACHABLE_COOLDOWN. Without it the ranking hands back the same
    -- highest-value egg every cycle and the run bangs on it forever - which is
    -- exactly what three consecutive cycles against the same Rhino looked like.
    function M.markUnreachable(uid)
        if uid then unreachable[tostring(uid)] = os.clock() end
    end

    function M.clearUnreachable(uid)
        if uid then unreachable[tostring(uid)] = nil end
    end

    -- Walked by ITSELF, not by the record list. V3.1's first version only
    -- expired an entry if that uid still appeared in the current snapshot, so
    -- an egg we stole that then despawned was never visited again and stayed
    -- suppressed for the rest of the session - a table that only grows.
    local function pruneStolen()
        local now = os.clock()
        for uid, at in pairs(stolen) do
            if (now - at) > K.STOLEN_FOR then stolen[uid] = nil end
        end
        for uid, at in pairs(unreachable) do
            if (now - at) > K.UNREACHABLE_FOR then unreachable[uid] = nil end
        end
    end

    ---------- value ----------

    local function calcValue(rec)
        local uid = rec.Uid
        local hit = valueCache[uid]
        if hit then return hit end

        local item = {
            Category  = rec.AssetCategory,
            Scale     = tonumber(rec.AssetScale) or 1,
            Mutations = rec.Mutations or {},
        }

        local v = 0
        if AssetEarnings then
            local ok, rate = pcall(AssetEarnings.LiveRatePerSecond, item, nil, nil, svc.LocalPlayer)
            if ok and type(rate) == "number" then
                v = rate
            else
                -- LiveRatePerSecond needs a player; this one does not.
                ok, rate = pcall(AssetEarnings.MutationOnlyRatePerSecond, item)
                if ok and type(rate) == "number" then v = rate end
            end
        end

        -- BOUNDED. V3.1 used a double-buffered valueCache/valueCacheNext pair
        -- rebuilt every pass, which was self-bounding but meant re-deriving
        -- every value on every pass that missed. A single cache with a ceiling
        -- keeps the values and cannot grow without limit; at ~55 field eggs the
        -- ceiling is never reached in practice, and if it ever is, dropping the
        -- lot costs one pass of recalculation.
        if valueCacheN >= K.VALUE_CACHE_MAX then
            log.warn("value cache hit %d entries - clearing", valueCacheN)
            valueCache, valueCacheN = {}, 0
        end
        valueCache[uid] = v
        valueCacheN = valueCacheN + 1
        return v
    end

    M.value = calcValue

    -- THE PET INSIDE, NOT THE EGG MODEL.
    --
    -- AssetCategory is an internal id; DisplayName is what the game shows and
    -- what a player can pick out of a list.
    local function displayName(rec)
        local dir = AssetsDir and AssetsDir[rec.AssetCategory]
        -- No data module = no pet name; a short uid still lets the user tell
        -- two eggs apart in the list and steal one by position.
        return (dir and dir.DisplayName) or rec.AssetCategory
            or ("Egg " .. tostring(rec.Uid or "?"):sub(1, 6))
    end

    -- The rarity's STABLE id, which is what a saved filter selection matches on.
    -- _id and DisplayName are the same string for all ten rarities today
    -- (Common..Divine, RarityNumber 1..10), so this exists to keep a future
    -- display rename from silently emptying somebody's Farm selection.
    local function rarityIdOf(rec)
        local dir = AssetsDir and AssetsDir[rec.AssetCategory]
        if dir and dir.Rarity then
            return dir.Rarity._id or dir.Rarity.DisplayName or "?"
        end
        return "?"
    end

    local function rarityOf(rec)
        local dir = AssetsDir and AssetsDir[rec.AssetCategory]
        if dir and dir.Rarity then
            return dir.Rarity.DisplayName or dir.Rarity._id or "?"
        end
        return "?"
    end

    -- The number on the egg in game: the pet base weight stretched by the
    -- egg own AssetScale. V3.1 uses it to tell two otherwise identical
    -- entries apart - Dodo 7.3kg and Dodo 20.4kg are the same pet and the
    -- same income.
    local function weightOf(rec)
        local dir = AssetsDir and AssetsDir[rec.AssetCategory]
        local base = dir and dir.Egg and tonumber(dir.Egg.WeightKg)
        if not base then return 0 end
        return base * (tonumber(rec.AssetScale) or 1)
    end

    -- Adaptive precision: two decimals under 10 of a unit, one above, so a
    -- list reads 6.78M and 15.8M rather than 6.8M and 15.8M.
    function M.formatRate(n)
        n = tonumber(n) or 0
        for _, u in ipairs({ { 1e12, "T" }, { 1e9, "B" }, { 1e6, "M" }, { 1e3, "K" } }) do
            if n >= u[1] then
                local v = n / u[1]
                local txt = (v < 10) and string.format("%.2f", v) or string.format("%.1f", v)
                return (txt:gsub("%.?0+$", "")) .. u[2]
            end
        end
        return tostring(math.floor(n))
    end

    ---------- raw snapshot ----------

    local function readField()
        local records = nil
        BX.try("eggs.readField", function()
            local data = EggState and EggState.ReadFieldEggs and EggState.ReadFieldEggs()
            if type(data) == "table" and type(data.Records) == "table" then
                records = data.Records
            end
        end)
        return records
    end

    -- Only when EggState has nothing - a fresh join before its background sync
    -- lands. This IS a descendants walk, so it is rate-limited to once every
    -- FALLBACK_TTL seconds and never runs while EggState is answering.
    local function readFallback()
        local now = os.clock()
        if (now - fallbackAt) < K.FALLBACK_TTL then return nil end
        fallbackAt = now
        stats.fallbacks = stats.fallbacks + 1

        local records = {}
        BX.try("eggs.fallback", function()
            local slots = workspace:FindFirstChild("AreaEggSlotsClient")
            if not slots then return end
            -- THE SLOT MODELS ARE NAMED BY UID. Measured on the live game:
            -- AreaEggSlotsClient holds one Model per field egg, named with the
            -- egg's uid ("fb48c510..." or "FirstAreaEgg_<owner>_<n>_Forest:
            -- Slot_002"), each with a Hitbox part - and NO Uid/EggUid
            -- attribute anywhere (0 of 527 descendants). The attribute read
            -- this used to do found nothing, so an executor without game-
            -- module require() had no eggs at all. Children only: one folder,
            -- ~60 models, no descendants walk.
            for _, m in ipairs(slots:GetChildren()) do
                if m:IsA("Model") then
                    local uid = m:GetAttribute("Uid") or m:GetAttribute("EggUid") or m.Name
                    local cf
                    local hit = m:FindFirstChild("Hitbox")
                    if hit and hit:IsA("BasePart") then cf = hit.CFrame else cf = m:GetPivot() end
                    if uid and cf then
                        -- A CFrame, deliberately - NOT the Instance. Holding
                        -- the part would keep a despawned egg's model alive in
                        -- the cache for as long as the cache lived.
                        records[#records + 1] = {
                            Uid = tostring(uid), BoundsCFrame = cf, State = "Slot",
                        }
                    end
                end
            end
        end)
        log.info("fallback scan: %d records from AreaEggSlotsClient (no EggState - names and values unavailable)", #records)
        return #records > 0 and records or nil
    end

    local function snapshot(force)
        local now = os.clock()
        if not force and rawSnap and (now - rawSnapAt) < dev.scale(K.RAW_TTL) then
            return rawSnap
        end
        local records = readField()
        if not records or #records == 0 then
            records = readFallback() or records
        end
        if records then
            rawSnap, rawSnapAt = records, now
        end
        return rawSnap
    end

    ---------- the list ----------

    -- opts.state    : which States count as takeable (default Slot + Dropped)
    -- opts.minValue : skip anything below this income/sec
    -- opts.filter   : function(rec, value) -> boolean, the caller's own rule
    function M.list(opts, force)
        opts = opts or {}
        local now = os.clock()

        local fresh = (now - listAt) < dev.scale(K.CACHE_TTL)
        local mayRebuild = (now - listAt) >= K.MIN_REBUILD
        if not force and list and fresh and not (dirty and mayRebuild) then
            stats.cacheHits = stats.cacheHits + 1
            return list
        end
        if dirty and mayRebuild then
            stats.dirtyRebuilds = (stats.dirtyRebuilds or 0) + 1
            dirty = false
        end

        local t0 = os.clock()
        local records = snapshot(force)

        -- The partial-snapshot guard. See the header.
        local n = records and #records or 0
        if n > K.FULL_FIELD_MIN then sawFullField = true end
        if sawFullField and n > 0 and n <= K.PARTIAL_FLOOR and list and #list > 0 then
            if not saidPartial then
                saidPartial = true
                stats.partialHeld = stats.partialHeld + 1
                log.info("only %d records replicated - field still loading, keeping the last %d",
                    n, #list)
            end
            return list
        end
        saidPartial = false

        if not records then
            list = list or {}
            listAt = now
            return list
        end

        pruneStolen()

        local TAKEABLE = opts.state or { Slot = true, Dropped = true }
        local out, seen = {}, {}
        local considered, dupes = 0, 0

        for _, rec in ipairs(records) do
            considered = considered + 1
            local uid = rec.Uid and tostring(rec.Uid)

            -- DEDUPED BY UID.
            --
            -- V3.1 built the list straight from Records with no uniqueness
            -- check. A uid appearing twice in a snapshot - which happens while
            -- the field is shifting - produced two entries for one egg, so the
            -- same egg could be picked, reported and counted twice. That is
            -- the duplicate-label problem.
            if uid and not seen[uid] then
                seen[uid] = true

                if not TAKEABLE[rec.State] then
                    -- not takeable right now
                elseif stolen[uid] then
                    -- ours already, or on its way
                elseif unreachable[uid] then
                    -- beat us recently; give it a rest rather than loop on it
                else
                    local value = calcValue(rec)
                    local pos = rec.BoundsCFrame and rec.BoundsCFrame.Position
                    if pos and (not opts.minValue or value >= opts.minValue)
                       and (not opts.filter or opts.filter(rec, value)) then
                        out[#out + 1] = {
                            uid   = uid,
                            state = rec.State,
                            pos   = pos,          -- Vector3, not an Instance
                            value = value,
                            name  = displayName(rec),
                            rarity = rarityOf(rec),
                            rarityId = rarityIdOf(rec),
                            -- The ESP needs it for the pet's icon and rarity
                            -- colour, both of which live in Data.Assets.
                            assetCategory = rec.AssetCategory,
                            -- The ESP lifts its card by the egg's size roll and
                            -- shows mutations on their own line, both V3.1.
                            assetScale = rec.AssetScale,
                            mutations = rec.Mutations,
                            kg    = weightOf(rec),
                            guardHeld = (rec.State == "GuardCarried"),
                            dropped   = (rec.State == "Dropped"),
                            -- Needed to build a FirstAreaSlotKey. Without it
                            -- the server refuses the carry outright for
                            -- first-area eggs.
                            areaId = rec.AreaId,
                            nestId = rec.NestId,
                        }
                    end
                end
            elseif uid then
                dupes = dupes + 1
            end
        end

        table.sort(out, function(a, b) return a.value > b.value end)

        -- KEEP THE VALUE CACHE NEAR THE SIZE OF THE FIELD.
        --
        -- Entries are only ever added, so as eggs are taken and respawn with
        -- new uids the cache drifts above the live field: measured at 110
        -- entries against a 55-egg field in one session. The 600 ceiling means
        -- it is bounded rather than leaking, but letting it drift to the cap
        -- and then dropping everything is a worse trade than pruning what is
        -- demonstrably gone.
        --
        -- Only runs when it has actually drifted, and only walks the cache -
        -- it is a hash of uid -> number, so this is cheap and rare.
        if valueCacheN > (#out * 2 + 50) then
            local keep, kept = {}, 0
            for _, e in ipairs(out) do
                local v = valueCache[e.uid]
                if v ~= nil then
                    keep[e.uid] = v
                    kept = kept + 1
                end
            end
            log.trace("value cache pruned %d -> %d (field %d)", valueCacheN, kept, #out)
            valueCache, valueCacheN = keep, kept
        end

        list, listAt = out, now
        stats.scans = stats.scans + 1
        stats.lastScanMs = (os.clock() - t0) * 1000
        stats.lastConsidered = considered
        stats.lastKept = #out

        -- ONE SUMMARY PER SCAN, not per frame. Everything needed to explain a
        -- bad pick: how long it took, how many records existed, how many
        -- survived the filter, how many were duplicates, and what won.
        log.trace("scan: %d records -> %d takeable (%d dupes) in %.1fms, best %s %s/s",
            considered, #out, dupes, stats.lastScanMs,
            out[1] and out[1].name or "-",
            out[1] and string.format("%.0f", out[1].value) or "-")

        return list
    end

    function M.best(opts)
        local l = M.list(opts)
        return l and l[1] or nil
    end

    -- A fresh read for ONE uid, straight past the cache. Used when the caller
    -- needs to know the current truth about a specific egg (has it moved, has
    -- someone else taken it) rather than what the last scan said.
    function M.get(uid)
        if not uid then return nil end
        local rec
        BX.try("eggs.get", function()
            rec = EggState and EggState.ReadFieldEgg and EggState.ReadFieldEgg(uid)
        end)
        if not rec then return nil end
        return {
            uid   = tostring(uid),
            state = rec.State,
            pos   = rec.BoundsCFrame and rec.BoundsCFrame.Position,
            value = calcValue(rec),
            name  = displayName(rec),
            rarity = rarityOf(rec),
            rarityId = rarityIdOf(rec),
            assetCategory = rec.AssetCategory,
            assetScale = rec.AssetScale,
            mutations = rec.Mutations,
            kg    = weightOf(rec),
            areaId = rec.AreaId,
            nestId = rec.NestId,
        }
    end

    -- WHAT ARE WE HOLDING RIGHT NOW, ACCORDING TO THE GAME.
    --
    -- Not a local flag. V3.1 learned this the hard way: heldEggUid is set by
    -- the callers of the carry, never by the carry itself, so there is a window
    -- where the game has handed us an egg and our own flag says otherwise.
    -- EggState is the authority.
    function M.carryingUid()
        local found
        BX.try("eggs.carryingUid", function()
            local data = EggState and EggState.ReadFieldEggs and EggState.ReadFieldEggs()
            for _, r in pairs(data and data.Records or {}) do
                if r.State == "Carried" then
                    found = tostring(r.Uid)
                    break
                end
            end
        end)
        return found
    end

    -- Still a valid thing to go and take?
    function M.stillTakeable(uid, states)
        local r = M.get(uid)
        if not r then return false, "gone" end
        local ok = (states or { Slot = true, Dropped = true })[r.state]
        return ok and true or false, r.state
    end

    function M.stats()
        local s = table.clone(stats)
        s.listSize = list and #list or 0
        s.valueCache = valueCacheN
        s.sawFullField = sawFullField
        return s
    end

    ---------- invalidation ----------

    -- EVENT-DRIVEN, NOT POLLED.
    --
    -- V3.1 leaned on a TTL and cleared BX.eggCache by hand from four different
    -- places. The field tells us when it changed, so the cache is dropped on
    -- the signal and the TTL is only a backstop for signals we do not get.
    -- THE SIGNALS ARE NOT RBXScriptSignals.
    --
    -- Read live off ReplicatedStorage.Client.EggState: every one of these is a
    -- TABLE with a Connect method, not a Roblox signal. An earlier version of
    -- this module tested typeof(sig) == "RBXScriptSignal" and therefore
    -- connected to NOTHING - the scope reported conns=0 and the cache was
    -- quietly running on its TTL alone. Two of the names were wrong as well
    -- (FieldEggShifted / FieldEggsChanged do not exist). Verified names below.
    --
    -- They return a connection with :Disconnect(), so the scope retires them
    -- the same as any other.
    local WATCH = {
        "CarryChanged",      -- an egg changed hands
        "FieldShifted",      -- the field moved
        "FieldRefreshed",    -- bulk refresh
        "FieldGone",         -- an egg left the field
        "FieldClaimed",      -- someone claimed one
        "SnapshotRefreshed", -- the underlying snapshot re-synced
    }

    local sc = BX.scope("features.eggs")
    local watched = 0
    if EggState then
        for _, name in ipairs(WATCH) do
            BX.try("eggs.watch." .. name, function()
                local sig = EggState[name]
                if sig and type(sig) == "table" and type(sig.Connect) == "function" then
                    sc:connect(sig, function() M.markDirty(name) end)
                    watched = watched + 1
                end
            end)
        end
    end
    log.info("watching %d/%d EggState signals", watched, #WATCH)

    -- A respawn does not change the field, but it does end any approach in
    -- progress, so the next read should be honest rather than half a second
    -- stale.
    BX.require("core.character").onSpawn(sc, "eggs.respawn", function()
        M.invalidate("respawn")
    end)

    return M
end)

--[[ ==== features/grab.lua =========================================== ]]
-- =============================================================================
-- FEATURES.GRAB: take the egg off the nest
-- =============================================================================
--
--     local grab = BX.require("features.grab")
--
--     local ok, info = grab.take(uid, {
--         pos    = Vector3,            -- where the egg is
--         cancel = function() end,     -- polled; true means stop now
--         tries  = 3,
--     })
--
--     grab.confirm(uid)   -- (bool, witness) - is this egg in our hands?
--
-- RESPONSIBILITY. It fires the prompt and decides whether we got the egg. It
-- does not travel to the egg, choose it, or carry it home.
--
-- WHY A PROMPT AND NOT THE REMOTE. The game's own steal goes through a
-- ProximityPrompt; invoking the remote directly produces a call shape the
-- legitimate client never makes.
--
-- THE PROMPTS ARE POOLED, NOT PER-EGG. Every steal prompt is
--     CarryAreaEgg[ProximityPrompt] < SmartPromptPart[Part] < Workspace
-- and there are ~54 SmartPromptParts directly under Workspace. The game MOVES
-- one to whichever egg you are standing at. They are not owned by an egg.
--
-- We arrive fast, so the game has often not handed a prompt to OUR egg yet.
-- V3.1's first version fired whichever enabled prompt was nearest to US, which
-- in that window is a stale one parked at a different nest: it fires, returns
-- true, and nothing is stolen. So we aim at the EGG, not at ourselves, and wait
-- briefly for a prompt to arrive rather than burning every attempt in half a
-- second.
--
-- CONFIRMATION IS ONE FUNCTION, ON PURPOSE.
--
-- In V3.1 the question "did we get it" was answered in several places that had
-- drifted apart - the grab checked one thing, the loop checked another, and
-- carryingUid() a third. This is the single ladder, ordered fastest-witness
-- first, and it is the only thing that decides.

BX.module("features.grab", function(BX)
    local svc  = BX.require("core.services")
    local data = BX.require("core.data")
    local exec = BX.require("core.exec")
    local ch   = BX.require("core.character")
    local dev  = BX.require("core.device")
    local eggs = BX.require("features.eggs")
    local log  = BX.require("boot.log").for_module("grab")

    local RunService = svc.RunService
    local M = {}

    local K = {
        PROMPT_CACHE   = 30,    -- how long the prompt list stays good; they do not move
        PROMPT_NEAR    = 14,    -- a prompt this close to the egg belongs to it
        PROMPT_WAIT    = 0.6,   -- wait for the game to hand a prompt over
        STEP_INSIDE    = 3,     -- studs inside MaxActivationDistance to stand
        CONFIRM_WINDOW = 1.2,   -- how long to wait for a witness after firing
        TRIES          = 3,
        RETRY_GAP      = 0.15,  -- never zero: a retry without a yield is a spin
        TP_PROMPT_WAIT = 1.2,   -- after a teleport, how long to wait for a prompt
    }
    M.K = K

    -- THROUGH core.data. A private FindFirstChild + require here bypassed
    -- the one place that knows whether this executor can require game
    -- modules at all, and cached a miss forever. core.data probes once,
    -- lets a miss expire, and backs off - see core/data.lua.
    local EggState = data.eggState()

    ---------- prompt cache ----------

    -- 38,016 DESCENDANTS, 12 MILLISECONDS, THREE TIMES PER GRAB.
    --
    -- Measured on a live server: Workspace:GetDescendants() returns 38k
    -- instances and takes 12.1ms to walk. V3.1's first version ran it once per
    -- grab ATTEMPT, so a three-attempt grab spent ~36ms blocking the main
    -- thread - while standing on a nest, which is both a visible hitch and time
    -- the guard uses to reach us.
    --
    -- There are only 61 ProximityPrompts in the place and the steal ones do not
    -- move, so the list is cached. No CollectionService tag exists for them
    -- (CarryAreaEgg / AreaEgg / CarryPrompt all checked, all empty), so the
    -- walk still has to happen - once every PROMPT_CACHE seconds rather than
    -- three times a second.
    local prompts, promptsAt = nil, 0

    BX.profile.watch("grab.prompts", function() return prompts and #prompts or 0 end)

    local function promptList()
        local now = os.clock()
        if prompts and (now - promptsAt) < K.PROMPT_CACHE then
            return prompts
        end
        local t0 = os.clock()
        local found = {}
        for _, d in ipairs(workspace:GetDescendants()) do
            if d:IsA("ProximityPrompt") then
                local txt = string.lower(tostring(d.ActionText) .. " "
                    .. tostring(d.ObjectText) .. " " .. d.Name)
                if txt:find("steal") or txt:find("carry") then
                    found[#found + 1] = d
                end
            end
        end
        prompts, promptsAt = found, now
        log.trace("prompt cache rebuilt: %d prompts in %.1fms", #found, (os.clock() - t0) * 1000)
        return prompts
    end

    local function promptPos(p)
        local parent = p.Parent
        if not parent then return nil end
        if parent:IsA("BasePart") then return parent.Position end
        if parent:IsA("Model") then return parent:GetPivot().Position end
        return nil
    end

    -- Wait for the game to hand a steal prompt to THIS egg.
    --
    -- Public because the teleport path needs it before the grab does: landing
    -- on a nest in one frame gets us there long before the game has moved a
    -- pooled prompt onto our egg, and firing into that gap does nothing three
    -- times over. Polls the cache we already keep - prompts are reassigned by
    -- changing their Parent, which is re-read every pass, so no rebuild is
    -- needed. Bounded, and it exits the moment one appears.
    function M.waitForPrompt(targetPos, cancel, seconds)
        if typeof(targetPos) ~= "Vector3" then return false end
        local listed = promptList()
        local t0 = os.clock()
        local until_ = t0 + dev.scale(seconds or K.TP_PROMPT_WAIT)
        repeat
            if cancel and cancel() then return false end
            for _, d in ipairs(listed) do
                if d.Parent and d.Enabled then
                    local pos = promptPos(d)
                    if pos and (pos - targetPos).Magnitude <= K.PROMPT_NEAR then
                        log.trace("prompt arrived after %.2fs", os.clock() - t0)
                        return true
                    end
                end
            end
            task.wait(0.05)
        until os.clock() > until_
        log.trace("prompt never showed after %.2fs", os.clock() - t0)
        return false
    end

    ---------- confirmation ----------

    -- Ordered fastest-witness first. Each returns a name, so the log says WHICH
    -- witness answered - and when a grab is wrongly confirmed, that name is the
    -- first clue.
    function M.confirm(uid, baseWalkSpeed, carrySignal)
        if carrySignal then return true, "CarryChanged" end

        -- The server drops WalkSpeed the moment it grants a carry.
        local hum = ch.humanoid()
        if hum and baseWalkSpeed and hum.WalkSpeed and hum.WalkSpeed < (baseWalkSpeed - 1) then
            return true, "walkspeed drop"
        end

        -- THE EGG IN YOUR HAND - BUT IT HAS TO BE THIS EGG.
        --
        -- V3.1's first version accepted any Tool with ItemType == "AssetEgg".
        -- Caught live: a leftover "Spideron Egg" tool was sitting in the
        -- character with a uid that was neither a field egg nor an owned one.
        -- Any grab made while that was in hand confirmed instantly without
        -- having picked anything up, and the loop flew home empty. The UID
        -- attribute must match, or the witness is worthless.
        local char = ch.get()
        if char then
            for _, c in ipairs(char:GetChildren()) do
                if c:IsA("Tool") and c:GetAttribute("ItemType") == "AssetEgg"
                   and tostring(c:GetAttribute("UID")) == tostring(uid) then
                    return true, "egg tool in hand"
                end
            end
        end

        local rec = eggs.get(uid)
        if rec and rec.state == "Carried" then return true, "ReadFieldEgg" end

        -- The whole-table read, which in V3.1's trace is what finally noticed
        -- while the single-uid read still said "Slot". Measured at 0.0001s, so
        -- it is a second opinion rather than a last resort.
        local any
        BX.try("grab.confirmAll", function()
            local data = EggState and EggState.ReadFieldEggs and EggState.ReadFieldEggs()
            for _, r in pairs(data and data.Records or {}) do
                if r.State == "Carried" and tostring(r.Uid) == tostring(uid) then
                    any = true
                    break
                end
            end
        end)
        if any then return true, "ReadFieldEggs" end

        return false, rec and rec.state or "unknown"
    end

    ---------- firing ----------

    local function fireAt(targetPos, cancel)
        if not exec.can.prompts then
            return false, "executor has no fireproximityprompt"
        end
        local hrp = ch.root()
        if not hrp then return false, "no root" end

        local listed = promptList()

        -- BUILD FIRST, POLL SECOND. V3.1 had these the other way round: it
        -- polled the cache for PROMPT_WAIT and on every miss set the cache
        -- timestamp to 0 to "force a rebuild" - but the rebuild is the block
        -- above, which had already run and would not run again until the call
        -- returned. So the wait polled an unchanged list a dozen times and on
        -- the first grab of a session polled an empty table. That is "it
        -- arrives and just goes home again", worst at low frame rates, which is
        -- exactly when the prompt has not been handed over yet.
        if typeof(targetPos) == "Vector3" then
            M.waitForPrompt(targetPos, cancel, K.PROMPT_WAIT)
            if cancel and cancel() then return false, "cancelled" end
        end

        local best, bestDist = nil, math.huge
        for _, d in ipairs(listed) do
            -- Enabled is re-checked live; only the SEARCH is cached.
            if d.Parent and d.Enabled then
                local pos = promptPos(d)
                if pos then
                    -- Must belong to the egg we came for. Without this we
                    -- happily fire a pooled prompt still parked at another nest.
                    local onTarget = (typeof(targetPos) ~= "Vector3")
                        or ((pos - targetPos).Magnitude <= K.PROMPT_NEAR)
                    local dist = (hrp.Position - pos).Magnitude
                    if onTarget and dist <= (d.MaxActivationDistance + 8) and dist < bestDist then
                        best, bestDist = d, dist
                    end
                end
            end
        end

        if not best then return false, "no prompt for this egg" end

        -- GET INSIDE THE RANGE THE SERVER CHECKS, NOT THE ONE WE CHECK.
        --
        -- This is "sometimes it just flies away without taking the egg", and it
        -- was a lie our own log told us. The search accepts a prompt up to
        -- MaxActivationDistance + 8 away - 16 studs - and fireproximityprompt
        -- returns true from all 16, because it is a local call. The SERVER
        -- re-checks against MaxActivationDistance (8, measured on every steal
        -- prompt here) and silently drops anything further. So the trace read
        -- "fired prompt at 12.4 studs -> true" three times, all three
        -- "succeeded", and no egg was ever handed over.
        local pos = promptPos(best)
        local limit = (best.MaxActivationDistance or 8) - K.STEP_INSIDE
        if pos and bestDist > limit then
            local from = hrp.Position
            local step = pos - from
            local want = pos - (step.Magnitude > 0.1 and step.Unit or Vector3.new(0, 0, 1))
                * math.max(limit * 0.5, 2)
            pcall(function()
                hrp.CFrame = CFrame.new(Vector3.new(want.X, from.Y, want.Z))
                hrp.AssemblyLinearVelocity = Vector3.zero
            end)
            RunService.Heartbeat:Wait()
            local h2 = ch.root()
            if h2 then bestDist = (h2.Position - pos).Magnitude end
        end

        -- RESTORED AFTERWARDS.
        --
        -- V3.1 set HoldDuration = 0 and RequiresLineOfSight = false and left
        -- them that way. These are POOLED objects the game reuses for every
        -- player-facing steal, so the edit persists on a shared instance for
        -- the rest of the session - a standing modification to game state that
        -- nothing ever undoes.
        local wasHold, wasLoS = best.HoldDuration, best.RequiresLineOfSight
        pcall(function()
            best.HoldDuration = 0
            best.RequiresLineOfSight = false
        end)
        local fired = exec.firePrompt(best, 0)
        if fired then exec.firePrompt(best) end
        pcall(function()
            best.HoldDuration = wasHold
            best.RequiresLineOfSight = wasLoS
        end)

        return fired and true or false,
            fired and ("fired at %.1f studs"):format(bestDist)
            or "fireproximityprompt failed",
            bestDist
    end

    ---------- take ----------

    local stats = { attempts = 0, taken = 0, failed = 0, cancelled = 0 }
    function M.stats() return table.clone(stats) end

    function M.take(uid, opts)
        opts = opts or {}
        local cancel = opts.cancel
        local tries  = opts.tries or K.TRIES
        local targetPos = opts.pos

        stats.attempts = stats.attempts + 1
        local t0 = os.clock()

        local hum0 = ch.humanoid()
        local baseWS = (hum0 and hum0.WalkSpeed and hum0.WalkSpeed > 0) and hum0.WalkSpeed or nil

        -- One connection, on a scope, dropped on every exit path. V3.1 built
        -- this connection inside a pcall and disconnected it in one place; an
        -- early return past that point leaked it, once per grab.
        local sc = BX.scope("features.grab.attempt")
        local carrySignal = false
        if EggState and EggState.CarryChanged then
            BX.try("grab.watchCarry", function()
                sc:connect(EggState.CarryChanged, function(info)
                    if type(info) ~= "table" or info.Uid == nil
                       or tostring(info.Uid) == tostring(uid) then
                        carrySignal = true
                    end
                end)
            end)
        end

        local function finish(ok, reason, attempt, fireDist)
            sc:destroy()
            local ms = (os.clock() - t0) * 1000
            if ok then
                stats.taken = stats.taken + 1
                eggs.markStolen(uid)
            elseif reason == "cancelled" then
                stats.cancelled = stats.cancelled + 1
            else
                stats.failed = stats.failed + 1
            end
            -- ONE LINE PER GRAB. Result, which witness confirmed it, how many
            -- attempts it took, how far from the prompt we fired, how long the
            -- whole thing took, and the device tier.
            local level = ok and log.info or log.warn
            level("%s uid=%s after %d/%d tries in %.0fms (witness=%s dist=%s tier=%s)",
                ok and "TAKEN" or ("FAILED: " .. tostring(reason)),
                tostring(uid), attempt or 0, tries, ms, tostring(reason),
                fireDist and string.format("%.1f", fireDist) or "-", dev.tier)
            return ok, {
                reason = reason, attempts = attempt or 0,
                ms = ms, distance = fireDist,
            }
        end

        -- Already holding it (a retry after a lost confirmation, say).
        local have, witness = M.confirm(uid, baseWS, carrySignal)
        if have then return finish(true, witness, 0) end

        for attempt = 1, tries do
            if cancel and cancel() then return finish(false, "cancelled", attempt) end
            if not ch.root() then return finish(false, "no character", attempt) end

            -- The egg may have gone while we were approaching. Checking here
            -- rather than firing blind is what stops us burning three attempts
            -- on something another player already took.
            local ok, state = eggs.stillTakeable(uid)
            if not ok and not carrySignal then
                return finish(false, "egg " .. tostring(state), attempt)
            end

            local fired, why, dist = fireAt(targetPos, cancel)
            if why == "cancelled" then return finish(false, "cancelled", attempt) end

            if fired then
                -- Wait for a witness rather than assuming. The server's grant
                -- and EggState's replication are a round trip apart.
                local until_ = os.clock() + dev.scale(K.CONFIRM_WINDOW)
                repeat
                    if cancel and cancel() then return finish(false, "cancelled", attempt, dist) end
                    local got, w = M.confirm(uid, baseWS, carrySignal)
                    if got then return finish(true, w, attempt, dist) end
                    RunService.Heartbeat:Wait()
                until os.clock() > until_
            end

            -- NEVER RETRY WITHOUT YIELDING. A failed attempt going straight
            -- round again is a hot loop hammering the prompt, which is both a
            -- frame cost and the shape of traffic that gets noticed.
            if attempt < tries then task.wait(dev.scale(K.RETRY_GAP)) end
        end

        local got, w = M.confirm(uid, baseWS, carrySignal)
        if got then return finish(true, w, tries) end
        return finish(false, "no confirmation", tries)
    end

    -- The prompt cache holds Instances, but they are the game's pooled prompts:
    -- long-lived, never per-egg, and re-checked for .Parent on every use, so a
    -- destroyed one is skipped rather than resurrected. It needs no scope of
    -- its own - BX.teardown clears BX._loaded, which takes this module and its
    -- cache with it.
    -- WARM IT BEFORE THE FIRST STEAL NEEDS IT.
    --
    -- The walk below is the one measured at 38,016 descendants / 12.1ms, and
    -- until something calls it the cache is empty - `grab.prompts=0` in the
    -- startup health line. That meant the FIRST steal always paid for it, in the
    -- middle of a cycle, while standing on a nest. It is a pure read, so it can
    -- just as well happen while the loading card is still up.
    --
    -- Returns the cost so the prewarm log can state it rather than claim it.
    function M.warmPrompts()
        local t0 = os.clock()
        local n = #promptList()
        return (os.clock() - t0) * 1000, n
    end

    function M.clearCache()
        prompts, promptsAt = nil, 0
    end

    return M
end)

--[[ ==== features/movement.lua ======================================= ]]
-- =============================================================================
-- FEATURES.MOVEMENT: get the character from here to there
-- =============================================================================
--
--     local move = BX.require("features.movement")
--
--     local ok, info = move.travel{
--         to       = Vector3,      -- where
--         speed    = 500,          -- studs/sec cruise
--         arrive   = 5,            -- close enough, in studs
--         carrying = false,        -- CALLER states this; movement never reads egg state
--         cancel   = function() end,  -- caller's abort predicate, polled per frame
--         tag      = "carry home", -- appears in every log line for this leg
--     }
--
--     move.descend("land")         -- straight down onto the ground
--     move.noclip(true/false)
--     move.setAnticheat(adapter)   -- optional; see ANTICHEAT SEAM below
--     move.stats()                 -- diagnostics
--
-- RESPONSIBILITY. This module moves a character and nothing else. It does not
-- know what an egg is, it does not decide where to go, and it does not read
-- anyone else's state. Whether we are carrying is an ARGUMENT, because that
-- changes how the leg is flown - not something it reaches into another module
-- to discover.
--
-- WHAT IS CARRIED OVER UNCHANGED FROM V3.1, AND WHY
--
-- These three were measured against the live game. They are not rewritten.
--
--   1. SUB-STEPPING (v186). Frame time is not discarded. The old mover clamped
--      dt to 0.05s and threw the rest away, so a low frame rate silently
--      scaled travel speed down with it:
--          20 fps -> 430 studs/s (100% of target)
--          15 fps -> 322        ( 75%)
--          10 fps -> 215        ( 50%)
--      The carry took twice as long as planned, the guard caught up, and the
--      egg went back to its nest. The clamp is KEPT for what it was actually
--      for - no single PivotTo may jump a large distance, because that climbs
--      geometry and flings you - but the full frame is now spent as several
--      small writes instead of one big one.
--
--   2. BANKED FRAME TIME (v210). A one-off hitch longer than MAX_FRAME still
--      had its excess thrown away, and every discarded millisecond makes the
--      trip longer in real time. On the longest carry that decides the run:
--          Titan Temple   4293 studs   8.6s at 500 studs/s
--          Cherry Blossom 3522         7.0s
--          everything else <= 1777     <= 3.6s
--      Titan is the only leg long enough for accumulated stalls to matter,
--      which is exactly why it was the only area failing on laggy clients and
--      passing on fast ones. Excess is banked and repaid over following
--      frames, still capped per frame so nothing teleports.
--      Measured result: Titan carry on a stuttering client 23.1s -> 9.5s.
--
--   3. THE OUTBOUND BRACKET (v212). Clients that cannot arm the spoof were
--      pinned at 500 studs/s outbound forever with a downward-only backoff.
--      It brackets toward the spoofed speed instead - double while legs come
--      back clean, halve the gap when the server pushes back, converging in
--      about three legs either way. Titan outbound 8.6s -> 3.6s.
--
-- ANTICHEAT SEAM. V3.1's mover called into the anticheat directly (BX.acPush,
-- BX.spoofAc, BX.relocAt, BX.legalWalkSpeed). That is the coupling we are not
-- repeating. Movement takes an OPTIONAL adapter and works without one:
--
--     move.setAnticheat{
--         push            = function(hrp, hum, velocity) end,
--         spoof           = function(claimedWalkSpeed, velocity) end,
--         legalWalkSpeed  = function() return n end,
--         lastRelocateAt  = function() return os.clock() or nil end,
--         allowance       = function() return studsPerSecond or nil end,
--         relocateCount   = function() return n end,
--     }
--
-- With no adapter the mover runs unspoofed and stays at NOSPOOF_FLOOR..500
-- outbound, which is the speed V3.1 measured as safe without a spoof. It does
-- NOT bracket upward blind: the bracket is judged on relocates, and with no
-- adapter there is nothing to judge.

BX.module("features.movement", function(BX)
    local svc = BX.require("core.services")
    local ch  = BX.require("core.character")
    local dev = BX.require("core.device")
    local rs  = BX.require("core.restore")
    local log = BX.require("boot.log").for_module("movement")

    local RunService, Players = svc.RunService, svc.Players
    local M = {}

    -- Movement's own tuning. Owned here, not in a shared table every module
    -- writes to - that shared K table is how V3.1 ended up with constants
    -- nobody could trace the owner of.
    local K = {
        GROUND_OFFSET     = 3,
        CRUISE_UP         = 18,    -- studs above the higher end to cruise at
        RAMP_FRAC         = 0.12,  -- share of flat distance spent climbing/diving
        RAMP_MAX          = 220,
        RAMP_MIN          = 40,    -- less than this and the climb is a vertical jerk
        START_SPEED       = 0.45,  -- fraction of cruise we leave the ground at
        SPEED_RAMP_FRAC   = 0.28,
        SLOW_RADIUS       = 50,    -- careful approach inside this
        SLOW_SPEED        = 260,
        ARRIVE            = 5,
        MAX_DT            = 0.05,  -- longest SINGLE write, in seconds
        MAX_FRAME         = 0.25,  -- most travel repaid in one frame
        MAX_DEBT          = 2.0,   -- most unspent time carried forward
        MAX_STEP          = 20,    -- ceiling on one write's displacement
        SPEED             = 1200,  -- outbound cruise ceiling
        SPEED_NOSPOOF     = 500,   -- measured safe without a spoof
        NOSPOOF_FLOOR     = 300,
        NOSPOOF_CONVERGE  = 40,
        DROP_SPEED        = 400,
        SPOOF_HEADROOM    = 1.35,  -- claimed WalkSpeed = speed * this
        WS_MAX            = 4000,
        WALKSPEED_SANE_MIN = 40,
        RELOC_CLAMP_FOR   = 6,
        RELOC_CLAMP_RATIO = 1.04,
        -- Teleport. TP_LANDED is how close counts as accepted;
        -- TP_SETTLE is the wait before measuring, because a refusal
        -- arrives as a relocate about 170ms later.
        TP_SETTLE         = 0.35,
        TP_LANDED         = 30,
    }
    M.K = K

    ---------- the anticheat seam ----------

    local ac = nil
    function M.setAnticheat(adapter) ac = adapter end
    local function acGet(name)
        local f = ac and ac[name]
        return type(f) == "function" and f or nil
    end

    ---------- ground ----------

    -- CACHED RAYCAST PARAMS.
    --
    -- V3.1 built a fresh RaycastParams and walked Players:GetPlayers() on every
    -- single call, and this is called on both ends of every leg plus the final
    -- ground snap. The filter only changes when the player list or our own
    -- character does, so it is rebuilt on those events rather than per call.
    local groundParams = RaycastParams.new()
    groundParams.FilterType = Enum.RaycastFilterType.Exclude
    groundParams.IgnoreWater = true

    local filterDirty = true
    local scratchIgnore = {}   -- reused; never reallocated per call

    local function rebuildFilter()
        -- EVERY player character, not just ours: other people's torsos and
        -- hats are CanCollide, so they read as solid ground. That is what
        -- stranded a carry 49 studs short of the plot for ten seconds.
        local n = 0
        for i = #scratchIgnore, 1, -1 do scratchIgnore[i] = nil end
        for _, pl in ipairs(Players:GetPlayers()) do
            if pl.Character then
                n = n + 1
                scratchIgnore[n] = pl.Character
            end
        end
        groundParams.FilterDescendantsInstances = scratchIgnore
        filterDirty = false
    end

    local function solidGroundY(pos)
        if filterDirty then rebuildFilter() end
        local origin = pos + Vector3.new(0, 80, 0)
        local dir = Vector3.new(0, -700, 0)
        -- A plain downward ray hits non-collidable markers first - Areas.Ground,
        -- GuardAreas.*.Bounds, Center, and worst of all "mainsky" - so it walks
        -- down through them until it finds something genuinely collidable.
        local extra = nil
        for _ = 1, 15 do
            local r = workspace:Raycast(origin, dir, groundParams)
            if not r then break end
            if r.Instance.CanCollide then
                if extra then groundParams.FilterDescendantsInstances = scratchIgnore end
                return r.Position.Y + K.GROUND_OFFSET
            end
            -- Non-collidable marker: exclude it and look again. Built on a
            -- copy so the shared scratch list is not permanently polluted.
            extra = extra or table.clone(scratchIgnore)
            extra[#extra + 1] = r.Instance
            groundParams.FilterDescendantsInstances = extra
        end
        if extra then groundParams.FilterDescendantsInstances = scratchIgnore end
        return nil
    end

    local function groundOr(pos, fallback)
        return solidGroundY(pos) or fallback
    end

    M.groundY = solidGroundY

    ---------- noclip ----------

    -- V3.1 kept `noclipOriginal` keyed by BasePart for the whole session and
    -- never cleared it, so every part of every character the player ever had
    -- stayed strongly referenced - a table that only grows, holding dead
    -- characters alive. The snapshot is per-enable and dropped on disable.
    local noclipSc, noclipWas, noclipParts, noclipFor = nil, nil, nil, nil

    local function noclipStep()
        local char = ch.get()
        if not char then return end
        if noclipFor ~= char or not noclipParts then
            -- Rebuilt only when the character changes. V3.1 walked
            -- GetDescendants on every physics step before this was cached.
            noclipParts, noclipFor, noclipWas = {}, char, {}
            for _, p in ipairs(char:GetDescendants()) do
                if p:IsA("BasePart") then
                    noclipParts[#noclipParts + 1] = p
                    noclipWas[p] = p.CanCollide
                end
            end
        end
        for i = 1, #noclipParts do
            local p = noclipParts[i]
            if p.Parent and p.CanCollide then p.CanCollide = false end
        end
    end

    function M.noclip(on)
        if on then
            if noclipSc then return end
            -- Registered so a terminal path restores collisions even if
            -- noclip(false) is never reached.
            rs.onRestore("movement.noclip", function() M.noclip(false) end)
            noclipSc = BX.scope("features.movement.noclip")
            noclipSc:onFrame("noclip", RunService.Stepped, noclipStep)
        else
            if not noclipSc then return end
            noclipSc:destroy()
            noclipSc = nil
            -- Disconnecting alone left every part CanCollide = false, so the
            -- character stayed non-collidable afterwards: it floats, and that
            -- is an obvious persistent tell.
            if noclipWas then
                for part, was in pairs(noclipWas) do
                    if part.Parent then pcall(function() part.CanCollide = was end) end
                end
            end
            noclipParts, noclipWas, noclipFor = nil, nil, nil
        end
    end

    ---------- the outbound speed bracket (v212) ----------

    local brk = { low = nil, high = nil, speed = nil, legSpeed = nil, legRelocs = nil }

    -- V3.1 runs the outbound leg at K.SPEED (1200) - arcTweenTo(eggPos,
    -- K.ARC_SPEED, "outbound", 4) - and gets there on the WalkSpeed claim
    -- alone, which needs nothing from an executor. So 1200 is the normal
    -- answer, not an optimistic one.
    --
    -- The bracket below is only for a leg that genuinely cannot spoof (one
    -- flown while carrying), and it still refuses to climb blind: with no way
    -- to tell a clean leg from a relocated one, it stays at the measured-safe
    -- 500 rather than guessing upward.
    function M.outboundSpeed()
        return K.SPEED
    end

    function M.carrySpeedCap()
        if not acGet("relocateCount") then return K.SPEED_NOSPOOF end
        return brk.speed or K.SPEED_NOSPOOF
    end

    local function bracketAfterLeg()
        local count = acGet("relocateCount")
        if not count or not brk.legSpeed then return end

        local used = brk.legSpeed
        local hadRelocs = count() > (brk.legRelocs or 0)

        -- BISECT, DO NOT CRAWL. A 100-a-leg climb takes eight trips to reach
        -- parity with a spoofed client - minutes of being slower for no reason.
        if hadRelocs then
            brk.high = used                                   -- too fast
        else
            brk.low = math.max(brk.low or K.SPEED_NOSPOOF, used)
        end

        local low = brk.low or K.SPEED_NOSPOOF
        local nextSpeed
        if brk.high then
            if (brk.high - low) <= K.NOSPOOF_CONVERGE then
                nextSpeed = low                               -- settled at the safe max
            else
                nextSpeed = math.floor((low + brk.high) / 2)
            end
        else
            nextSpeed = math.min(K.SPEED, low * 2)
        end

        nextSpeed = math.clamp(nextSpeed, K.NOSPOOF_FLOOR, K.SPEED)
        if nextSpeed ~= (brk.speed or K.SPEED_NOSPOOF) then
            log.info("travel: %s at %d - next leg %d studs/s (bracket %d..%s)",
                hadRelocs and "relocated" or "clean", used, nextSpeed,
                low, tostring(brk.high or "-"))
        end
        brk.speed = nextSpeed
        brk.legSpeed = nil
    end

    -- DECLARED ABOVE ITS FIRST USE, DELIBERATELY.
    --
    -- This sat below M.teleport, and a local referenced above its declaration
    -- is not an error in Lua - the name silently resolves to a global instead.
    -- Roblox happens to have a legacy global called `stats`, so the teleport
    -- path indexed a function and threw "attempt to index function with
    -- 'teleports'" the first time it ran. Nothing structural catches this.
    local stats = { legs = 0, cancelled = 0, respawned = 0, timedOut = 0, arrived = 0,
                    teleports = 0, tpLanded = 0, tpRefused = 0 }
    function M.stats() return table.clone(stats) end

    ---------- instant teleport ----------

    -- THE TRANSITION TO THE SELECTED TARGET IS A TELEPORT, NOT A TWEEN.
    --
    -- This is deliberate V3.1 behaviour and it is not cosmetic. Measured on
    -- this client: tweening 3029 studs to the target takes 6.0s at 500 studs/s,
    -- and the character DIED partway through it - six seconds crossing a field
    -- full of guards is the exposure the teleport exists to remove. The jump
    -- puts us on the nest in one frame.
    --
    -- It is only ever used EMPTY-HANDED. The carry-void rule that reverts a
    -- fast-moving egg cannot apply to a hand with no egg in it, which is why
    -- the outbound leg is the one place a jump has a chance at all. The carry
    -- home stays a tween.
    --
    -- THE SERVER DOES NOT ALWAYS ACCEPT IT. V3.1 measured five Prehistoric
    -- trips: one landed, one landed then was relocated 0.1s later, three were
    -- refused outright. So this never assumes - it jumps, settles, and MEASURES
    -- the gap. The caller falls back to the tween when it was refused.
    function M.teleport(pos, tag)
        local char, hrp = ch.get(), ch.root()
        if not char or not hrp then return false, math.huge end

        local gy = solidGroundY(pos)
        local dest = Vector3.new(pos.X, gy or pos.Y, pos.Z)
        local from = hrp.Position

        local ok = pcall(function() char:PivotTo(CFrame.new(dest)) end)
        if ok then
            hrp.AssemblyLinearVelocity = Vector3.zero
            hrp.AssemblyAngularVelocity = Vector3.zero
        end

        -- Settle before measuring. A relocate arrives about 170ms after a bare
        -- PivotTo, so reading the position immediately would call a refused
        -- jump a success.
        task.wait(dev.scale(K.TP_SETTLE))

        local h2 = ch.root()
        local gap = h2 and (h2.Position - dest).Magnitude or math.huge
        local landed = gap <= K.TP_LANDED

        stats.teleports = stats.teleports + 1
        if landed then
            stats.tpLanded = stats.tpLanded + 1
        else
            stats.tpRefused = stats.tpRefused + 1
        end

        log.info("tp %s: %.0f studs -> %s (%.0f off, tier=%s)",
            tostring(tag), (dest - from).Magnitude,
            landed and "landed" or "REFUSED", gap, dev.tier)

        return landed, gap
    end

    ---------- travel ----------

    -- Hoisted out of the per-sub-step loop. V3.1 built a fresh closure for
    -- every sub-step of every frame here - at 60fps with up to five sub-steps
    -- that is hundreds of throwaway closures a second, inside the hottest loop
    -- in the hub, which is exactly where allocations turn into GC pauses.
    local function writeStep(char, hum, hrp, dest, look)
        if hum then hum:Move(Vector3.zero, false) end
        char:PivotTo(CFrame.lookAt(dest, dest + look))
        hrp.AssemblyLinearVelocity = Vector3.zero
        hrp.AssemblyAngularVelocity = Vector3.zero
    end


    function M.travel(opts)
        local pos      = opts.to
        local tag      = opts.tag or "leg"
        local arrive   = opts.arrive or K.ARRIVE
        local carrying = opts.carrying and true or false
        local cancel   = opts.cancel

        local char = ch.get()
        local hrp  = ch.root()
        local hum  = ch.humanoid()
        if not char or not hrp then
            log.warn("%s: no character to move", tag)
            return false, { reason = "no-character" }
        end

        local speed = math.max(opts.speed or K.SPEED_NOSPOOF, 40)

        local start = hrp.Position
        local flatTotal = Vector3.new(pos.X - start.X, 0, pos.Z - start.Z).Magnitude
        if flatTotal < 1 then return true, { reason = "already-there", distance = 0 } end

        -- Cruise altitude: above whichever end sits higher, so the flat middle
        -- clears the ground between them.
        local startGround = groundOr(start, start.Y)
        local endGround   = groundOr(pos, pos.Y)
        local landY   = endGround
        local cruiseY = math.max(startGround, endGround, start.Y, pos.Y) + K.CRUISE_UP

        -- On a short hop there is not room for a full climb and dive, so they
        -- share the distance rather than overlapping.
        local ramp = math.clamp(flatTotal * K.RAMP_FRAC, K.RAMP_MIN, K.RAMP_MAX)
        if ramp * 2 > flatTotal * 0.9 then ramp = flatTotal * 0.45 end
        if flatTotal < K.RAMP_MIN * 2 then cruiseY = math.max(start.Y, pos.Y) end

        local wasPS = hum and hum.PlatformStand or false
        if hum then
            -- Captured ONCE per run, before anything touches it, so a cancelled
            -- or respawned leg still restores what the player actually had.
            rs.remember("movement.platformStand",
                function() return hum.PlatformStand end,
                function(v) hum.PlatformStand = v end)
            hum.PlatformStand = true
        end

        -- SPOOF WHENEVER WE ARE EMPTY-HANDED. NO ADAPTER REQUIRED.
        --
        -- V3.1 line 5687 is  spoof = BX.arcSpoof and hum and (heldEggUid == nil)
        -- with BX.arcSpoof = true, so the spoof is ON for every empty-handed
        -- leg, unconditionally. An earlier version of this module required an
        -- anticheat adapter to exist before spoofing, which inverted that
        -- default: with no adapter the outbound ran at the 500 no-spoof floor
        -- instead of 1200, and a 4200-stud leg took 8.4s instead of 3.5s. That
        -- is the "it visibly travels across the map" difference.
        --
        -- The spoof that actually works is the WalkSpeed claim below. The
        -- adapter's push/spoof are optional enrichment on top of it, NOT a
        -- precondition - see the note on the dead anticheat path in
        -- docs/ANTICHEAT-LEGACY.md.
        local push, spoofFn = acGet("push"), acGet("spoof")
        local spoof = (not carrying) and hum and true or false
        local claimWS, savedWS = nil, nil
        if spoof then
            rs.remember("movement.walkSpeed",
                function() return hum.WalkSpeed end,
                function(v) hum.WalkSpeed = v end)
            savedWS = hum.WalkSpeed
            claimWS = math.clamp(speed * K.SPOOF_HEADROOM, 16, K.WS_MAX)
            hum.WalkSpeed = claimWS
        end

        -- Record what this leg ran at, for the bracket to judge afterwards.
        if not carrying and not spoof then
            brk.legSpeed = speed
            local count = acGet("relocateCount")
            brk.legRelocs = count and count() or 0
        end

        local legAt = os.clock()
        local t0 = legAt
        local deadline = t0 + math.max(flatTotal / speed, 0.3) * 3 + 6
        local lastT = t0
        local arcDebt = 0
        local ok, reason = false, "timeout"
        local frames, subStepTotal, maxFrameSeen = 0, 0, 0

        log.trace("%s: begin %.0f studs at %.0f studs/s (carrying=%s spoof=%s tier=%s)",
            tag, flatTotal, speed, tostring(carrying), tostring(spoof), dev.tier)

        while os.clock() < deadline do
            if cancel and cancel() then reason = "cancelled" break end

            -- RE-RESOLVE THE CHARACTER EVERY FRAME.
            --
            -- V3.1 captured char and hum once and only re-read the root part.
            -- A respawn mid-leg left it writing PivotTo to a destroyed model
            -- inside a pcall that swallowed the error, so it span until the
            -- deadline - seconds of doing nothing - and then reported failure
            -- with no indication why. A changed character ends the leg at once
            -- and says so.
            local liveChar = ch.get()
            if liveChar ~= char then
                reason = "respawned"
                break
            end
            local hh = ch.root()
            if not hh then reason = "lost-root" break end

            local now = os.clock()
            local raw = now - lastT
            lastT = now

            -- Sub-stepping (v186) + banked frame time (v210). See the header.
            arcDebt = math.min(arcDebt + raw, K.MAX_DEBT)
            local frameDt = math.min(arcDebt, K.MAX_FRAME)
            arcDebt = arcDebt - frameDt
            if raw > maxFrameSeen then maxFrameSeen = raw end

            local subSteps = math.max(1, math.ceil(frameDt / K.MAX_DT))
            local dt = frameDt / subSteps
            frames = frames + 1
            subStepTotal = subStepTotal + subSteps

            local flat = Vector3.new(pos.X - hh.Position.X, 0, pos.Z - hh.Position.Z)
            local rem = flat.Magnitude
            if rem <= arrive then ok, reason = true, "arrived" break end

            local done = math.max(flatTotal - rem, 0)

            -- SPEED: ramp up over the first stretch, hold, ramp down over the
            -- last, then crawl the final approach.
            local want
            local speedRamp = math.max(ramp * K.SPEED_RAMP_FRAC, 1)
            if rem <= K.SLOW_RADIUS then
                want = math.min(K.SLOW_SPEED, speed)
            elseif rem < ramp then
                local f = rem / ramp
                want = math.max(speed * f, math.min(K.SLOW_SPEED, speed))
            elseif done < speedRamp then
                want = speed * (K.START_SPEED + (1 - K.START_SPEED) * (done / speedRamp))
            else
                want = speed
            end

            -- WHILE IT IS RELOCATING US, MOVE AT THE SPEED IT ALLOWS.
            --
            -- Travelling at the permitted speed is FASTER than being dragged
            -- back five times a second: V3.1 measured 1363 studs taking 9.41s
            -- and still finishing 417 short while being relocated ninety times.
            -- A client where the spoof holds never reaches this.
            --
            -- A carry only answers to relocates that happened DURING the carry.
            -- A relocate on the empty-handed hop out used to leave the whole
            -- trip home crawling at the ~216 allowance - below most guards'
            -- starting speed - which was "it goes slow the moment it steals".
            local lastReloc = acGet("lastRelocateAt")
            local relocAt = lastReloc and lastReloc() or nil
            if relocAt and (not carrying or relocAt >= legAt)
               and (os.clock() - relocAt) < K.RELOC_CLAMP_FOR then
                local allowFn = acGet("allowance")
                local allow = allowFn and allowFn() or nil
                if not allow and hum and hum.WalkSpeed > K.WALKSPEED_SANE_MIN then
                    allow = hum.WalkSpeed * K.RELOC_CLAMP_RATIO
                end
                if allow and allow > 0 and want > allow then
                    want = allow
                end
            end

            -- HEIGHT: climb over the first ramp, hold, dive over the last onto
            -- the target's own Y rather than the cruise.
            local wantY
            if done < ramp then
                wantY = start.Y + (cruiseY - start.Y) * (done / ramp)
            elseif rem < ramp then
                wantY = landY + (cruiseY - landY) * (rem / ramp)
            else
                wantY = cruiseY
            end

            -- One frame's travel, as `subSteps` writes of the size the desktop
            -- path has always used.
            local arrived = false
            for _ = 1, subSteps do
                local hp = hh.Position
                local f2 = Vector3.new(pos.X - hp.X, 0, pos.Z - hp.Z)
                local rem2 = f2.Magnitude
                if rem2 <= arrive then arrived = true break end

                local step = math.min(rem2, want * dt, K.MAX_STEP)
                local unit = f2.Unit
                local nxt = hp + unit * step
                pcall(writeStep, char, hum, hh,
                    Vector3.new(nxt.X, wantY, nxt.Z), unit)
            end
            if arrived then ok, reason = true, "arrived" break end

            if spoof then
                -- RE-ASSERT THE CLAIM EVERY FRAME. Setting WalkSpeed once is
                -- not enough: the game's governor writes it back about once a
                -- second, and the moment it lands we are travelling at cruise
                -- with a legal WalkSpeed behind us - the contradiction the
                -- validator relocates for.
                if hum.WalkSpeed < claimWS - 1 then hum.WalkSpeed = claimWS end
                -- Optional enrichment only. Both are no-ops on the current
                -- game version (the anticheat state table it edited no longer
                -- exists), so the claim above is what carries the leg.
                if push or spoofFn then
                    local told = flat.Unit * math.min(want, claimWS)
                    if push then push(hh, hum, told) else spoofFn(claimWS, told) end
                end
                pcall(function() hh.AssemblyLinearVelocity = Vector3.zero end)
            end

            -- YIELD. THIS LINE IS THE WHOLE "IT FREEZES MY GAME". Without it
            -- there is no frame boundary, so dt is ~0, step is ~0, the distance
            -- never falls, and it spins until the deadline with the client
            -- locked solid.
            RunService.Heartbeat:Wait()
        end

        ---------- settle ----------

        local hz = ch.root()
        local liveChar = ch.get()
        if hz and liveChar == char then
            local gy = solidGroundY(hz.Position)
            if gy and math.abs(hz.Position.Y - gy) > 1 then
                pcall(function() char:PivotTo(CFrame.new(hz.Position.X, gy, hz.Position.Z)) end)
            end
        end

        if spoof and hum and hum.Parent then
            -- Leaving an inflated WalkSpeed behind is what voids the next
            -- delivery. Always restored, on every exit path including the
            -- cancelled and respawned ones.
            local legalFn = acGet("legalWalkSpeed")
            local legal = legalFn and legalFn() or savedWS or 16
            pcall(function() hum.WalkSpeed = math.max(legal, 16) end)
        end
        if hum and hum.Parent then
            hum.PlatformStand = wasPS
            local hstate = hum:GetState()
            if hstate == Enum.HumanoidStateType.Freefall
               or hstate == Enum.HumanoidStateType.PlatformStanding
               or hstate == Enum.HumanoidStateType.Physics then
                pcall(function() hum:ChangeState(Enum.HumanoidStateType.Landed) end)
            end
        end
        if hz then
            hz.AssemblyLinearVelocity = Vector3.zero
            hz.AssemblyAngularVelocity = Vector3.zero
        end

        if not carrying and not spoof then bracketAfterLeg() end

        local gap = hz and Vector3.new(pos.X - hz.Position.X, 0, pos.Z - hz.Position.Z).Magnitude
            or math.huge
        local elapsed = os.clock() - t0
        local settled = ok or gap <= arrive + 4

        stats.legs = stats.legs + 1
        stats[settled and "arrived" or (reason == "cancelled" and "cancelled")
            or (reason == "respawned" and "respawned") or "timedOut"] =
            (stats[settled and "arrived" or (reason == "cancelled" and "cancelled")
            or (reason == "respawned" and "respawned") or "timedOut"] or 0) + 1

        -- ONE LINE THAT ANSWERS "WHY DID IT FAIL".
        --
        -- Distance, wall time, the speed asked for, how short it finished, why
        -- it stopped, the frame rate it actually got and the worst single
        -- frame. "Auto Steal froze while carrying" is answerable from this
        -- without guessing: a respawned reason, a timeout with a large gap, or
        -- a maxFrame of 0.8s each point somewhere different.
        local level = settled and log.trace or log.warn
        level("%s: %s %.0f studs in %.2fs (want %.0f/s, %.0f/s actual, %.1f short) "
            .. "reason=%s frames=%d sub=%.1f worstFrame=%.0fms tier=%s",
            tag, settled and "ok" or "FAILED", flatTotal, elapsed, speed,
            flatTotal / math.max(elapsed, 0.001), gap, reason, frames,
            frames > 0 and (subStepTotal / frames) or 0,
            maxFrameSeen * 1000, dev.tier)

        return settled, {
            reason = reason, distance = flatTotal, elapsed = elapsed,
            gap = gap, frames = frames, worstFrameMs = maxFrameSeen * 1000,
        }
    end

    ---------- descend ----------

    -- Straight down onto the ground where we are standing: the last step of the
    -- cycle, over the middle of the pen and then down to ground level.
    function M.descend(tag)
        tag = tag or "land"
        local char, h = ch.get(), ch.root()
        if not char or not h then return false end
        local hum = ch.humanoid()

        local gy = solidGroundY(h.Position)
        if not gy then
            -- Nothing under us to land on: give physics it back and fall.
            if hum then hum.PlatformStand = false end
            log.trace("%s: no ground below - falling", tag)
            return false
        end

        local x, z = h.Position.X, h.Position.Z
        local from = h.Position.Y
        if from - gy <= 2 then
            if hum then hum.PlatformStand = false end
            return true
        end

        if hum then hum.PlatformStand = true end
        local t0 = os.clock()
        local dur = math.clamp((from - gy) / math.max(K.DROP_SPEED, 50), 0.05, 1.2)
        while os.clock() - t0 < dur do
            if ch.get() ~= char then break end
            local hh = ch.root()
            if not hh then break end
            local f = (os.clock() - t0) / dur
            local y = from + (gy - from) * f
            pcall(function()
                char:PivotTo(CFrame.new(x, y, z) * (hh.CFrame - hh.CFrame.Position))
                hh.AssemblyLinearVelocity = Vector3.zero
            end)
            RunService.Heartbeat:Wait()
        end

        if ch.get() == char then
            pcall(function() char:PivotTo(CFrame.new(x, gy, z)) end)
        end
        if hum and hum.Parent then
            hum.PlatformStand = false
            pcall(function() hum:ChangeState(Enum.HumanoidStateType.Landed) end)
        end
        log.trace("%s: descended %.0f studs to ground", tag, from - gy)
        return true
    end

    ---------- lifecycle ----------

    -- The filter is rebuilt on player churn rather than on every raycast.
    local sc = BX.scope("features.movement")
    sc:connect(Players.PlayerAdded, function() filterDirty = true end)
    sc:connect(Players.PlayerRemoving, function() filterDirty = true end)
    ch.onSpawn(sc, "movement.respawn", function()
        filterDirty = true
        -- The old character's parts are gone; drop the noclip snapshot so it
        -- is rebuilt rather than held.
        noclipParts, noclipWas, noclipFor = nil, nil, nil
    end)

    -- Movement must never be left mid-leg with PlatformStand on and an
    -- inflated WalkSpeed. Callers cancel their own legs; this is the backstop
    -- for a re-execute or an unload.
    -- NEVER GUESS A WALKSPEED.
    --
    -- This used to end with
    --     hum.WalkSpeed = math.max(legalFn and legalFn() or 16, 16)
    -- and with no anticheat adapter legalFn is nil, so every run finished by
    -- setting WalkSpeed to 16. Measured on a live character whose real value
    -- was 209.8 - that is the "my character is so slow now" report, and it was
    -- the cleanup causing it.
    --
    -- Restoration is core.restore's job now: it holds the value captured
    -- before the first change. This only undoes what has no single property to
    -- read back.
    function M.reset()
        M.noclip(false)
    end

    return M
end)

--[[ ==== features/humanoid.lua ======================================= ]]
-- =============================================================================
-- FEATURES.HUMANOID: swap the Humanoid the anticheat is holding
-- =============================================================================
--
--     local hsw = BX.require("features.humanoid")
--     hsw.arm()      -- swap now, and on every respawn
--     hsw.disarm()
--     hsw.isSwapped()
--
-- THIS IS WHY V3.1 SURVIVES THE TELEPORT AND AN UNPORTED BUILD DOES NOT.
--
-- V3.1's own note, above BX.swapHumanoid:
--
--     Players.<you>.PlayerScripts.Game.ObbyAntiTPClient caches the Humanoid
--     ONCE, at CharacterAdded (its line 470), into an upvalue it later uses
--     in punish().
--
-- So the anticheat holds a direct reference to the Humanoid it saw when the
-- character spawned, and punishes through it. Replace that Humanoid with a
-- clone and destroy the original, and the reference the anticheat is holding
-- points at a destroyed object - punish() has nothing to act on. We never touch
-- the anticheat script itself, so there is nothing for it to detect.
--
-- Without this, an instant teleport is punished and the character dies a second
-- or two later. That is exactly what a port missing this looks like: the bait
-- lands, the teleport lands, and then the run dies for no visible reason.
--
-- WHAT THE CLONE CHANGES
--
--   * Dead, FallingDown and Ragdoll states disabled
--   * Health restored to MaxHealth
--   * an Animator is guaranteed, or animations silently stop
--   * the Health SCRIPT is destroyed first - it re-asserts server health onto
--     the humanoid, which would undo the above
--
-- THIS IS NOT REVERSIBLE, AND THAT IS HONEST RATHER THAN HIDDEN.
--
-- The original Humanoid is destroyed; there is nothing to put back. disarm()
-- therefore only stops us re-applying on the next respawn - the current
-- character keeps the swapped Humanoid until it next respawns naturally, at
-- which point the game builds a normal one. V3.1 behaves the same way.

BX.module("features.humanoid", function(BX)
    local svc = BX.require("core.services")
    local ch  = BX.require("core.character")
    local rs  = BX.require("core.restore")
    local log = BX.require("boot.log").for_module("humanoid")

    local M = {}

    local SWAP_ATTR = "BlyxoStealHum"
    M.SWAP_ATTR = SWAP_ATTR

    local sc = nil
    -- The originals captured at the FIRST swap of this character, so disarm can
    -- put them back without going through the restore ledger.
    local swapPrior = nil
    local stats = { swaps = 0, alreadySwapped = 0, failures = 0 }
    function M.stats() return table.clone(stats) end

    function M.isSwapped()
        local hum = ch.humanoid()
        return hum ~= nil and hum:GetAttribute(SWAP_ATTR) == true
    end

    -- THE STATES THE SWAP TURNS OFF ARE THE ONES THAT HAVE TO COME BACK.
    --
    -- The swap disables Dead, FallingDown and Ragdoll on the clone, and clears
    -- BreakJointsOnDeath, so the character cannot die during a run - which is
    -- the point, because the bait deliberately takes a guard hit. None of it was
    -- ever restored, and the clone outlives the run: after one Auto Steal the
    -- character was left permanently unkillable.
    --
    -- THIS IS WHY JUMPING STAYS BROKEN, and it is entirely our own state.
    --
    -- A character that cannot die cannot respawn, and a respawn is what gives
    -- the player a normal Humanoid again - the one the game's own controls are
    -- given at CharacterAdded. Leaving Dead disabled removes the player's only
    -- route back to a working jump and takes reset-character away with it.
    -- Putting these four values back does not touch the game's code, its
    -- security, or anything Roblox owns; it restores what the hub changed and
    -- lets the normal respawn path work again.
    --
    -- Written to whatever Humanoid is live AT RESTORE TIME, and only while it is
    -- still one of ours: a natural respawn has already produced a clean Humanoid
    -- and must not be written to on the strength of a value read from a
    -- character two lives ago.
    local function applyStates(prior)
        local hum = ch.humanoid()
        if not hum or hum:GetAttribute(SWAP_ATTR) ~= true then return end
        hum:SetStateEnabled(Enum.HumanoidStateType.Dead, prior.dead)
        hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, prior.fallingDown)
        hum:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, prior.ragdoll)
        hum.BreakJointsOnDeath = prior.breakJoints
    end

    local function rememberStates(prior)
        -- read returns the captured originals rather than reading the clone,
        -- which by this point already has our values on it.
        rs.remember("humanoid.states",
            function() return prior end,
            function(v) applyStates(v) end)
    end

    -- Returns true when the character ends up with a swapped Humanoid.
    function M.swap(char)
        char = char or ch.get()
        if not char then return false end

        local hum = char:FindFirstChildOfClass("Humanoid")
        if not hum then return false end

        if hum:GetAttribute(SWAP_ATTR) == true then
            stats.alreadySwapped = stats.alreadySwapped + 1
            return true
        end

        -- CAPTURED BEFORE ANYTHING IS CHANGED, read off the ORIGINAL.
        --
        -- Everything below this line is a value the hub sets and used to keep
        -- forever. The clone inherits them, the clone outlives the run, and
        -- nothing ever put them back - so a character that had been through one
        -- Auto Steal stayed unable to die for the rest of the session. See
        -- restoreStates() for why that is the jump report.
        local prior = {
            dead        = hum:GetStateEnabled(Enum.HumanoidStateType.Dead),
            fallingDown = hum:GetStateEnabled(Enum.HumanoidStateType.FallingDown),
            ragdoll     = hum:GetStateEnabled(Enum.HumanoidStateType.Ragdoll),
            breakJoints = hum.BreakJointsOnDeath,
        }

        local ok = BX.try("humanoid.swap", function()
            -- The Health script re-asserts server health onto the humanoid, so
            -- it has to go before the clone, or it simply undoes the swap.
            local healthScript = char:FindFirstChild("Health")
            if healthScript then healthScript:Destroy() end

            hum.BreakJointsOnDeath = false
            hum.Archivable = true

            local clone = hum:Clone()
            if not clone then error("clone failed") end
            clone.Name = "Humanoid"
            clone:SetAttribute(SWAP_ATTR, true)
            clone:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
            clone:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
            clone:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
            clone.Health = hum.MaxHealth

            -- An Animator must exist or animations silently stop.
            if not clone:FindFirstChildOfClass("Animator") then
                Instance.new("Animator").Parent = clone
            end

            -- The destroy is the whole point: this is the object the anticheat
            -- cached at CharacterAdded.
            hum:Destroy()
            clone.Parent = char

            if workspace.CurrentCamera then
                workspace.CurrentCamera.CameraSubject = clone
            end

            -- Re-seat the Animate script against the new humanoid.
            local animate = char:FindFirstChild("Animate")
            if animate then
                local ac = animate:Clone()
                animate:Destroy()
                ac.Parent = char
                ac.Disabled = false
            end

            -- Keep the joints alive.
            for _, d in ipairs(char:GetDescendants()) do
                if d:IsA("Motor6D") then d.Enabled = true end
            end
        end)

        if ok then
            -- Declared, not hidden: the original Humanoid and the Health script
            -- are destroyed, so the audit should say so rather than report a
            -- clean pass over a character that is permanently altered.
            rs.permanent("humanoid.swap",
                "Humanoid replaced and Health script destroyed - undone by respawn")

            -- WHAT *CAN* BE PUT BACK, IS PUT BACK. Registered with the same
            -- restore ledger as every other captured value, so restoreAll() and
            -- audit() cover it instead of it living only in disarm().
            swapPrior = prior
            rememberStates(prior)
            stats.swaps = stats.swaps + 1
            log.info("swapped (anticheat now holds a destroyed Humanoid)")
        else
            stats.failures = stats.failures + 1
            log.error("swap FAILED - teleports will be punished")
        end
        return ok and true or false
    end

    function M.isArmed() return sc ~= nil end

    function M.arm()
        if sc then return true end
        sc = BX.scope("features.humanoid")

        M.swap()

        -- UNCONDITIONALLY ON EVERY RESPAWN. A new character gets a fresh
        -- Humanoid and the anticheat caches THAT one at CharacterAdded, so a
        -- swap that happened once protects exactly one life. V3.1 re-applies
        -- on every spawn for this reason and says so at its respawn handler:
        -- "apply unconditionally, the reference build does this on every spawn".
        ch.onSpawn(sc, "humanoid.reswap", function(char)
            -- The new character's Humanoid is the game's own, and the values
            -- read off the previous one do not describe it. core.restore drops
            -- its entry on the same event for the same reason.
            swapPrior = nil
            M.swap(char)
        end)

        return true
    end

    function M.disarm()
        -- BEFORE the armed check, and unconditionally: the states outlive the
        -- run whether or not this module is still holding a scope, and a
        -- character left unable to die is the thing being undone here.
        --
        -- The Humanoid itself cannot be put back - that stays declared as
        -- permanent. Every VALUE we set on it can be, and core.restore's own
        -- restoreAll() will ask for the same thing again during the run's
        -- cleanup; applyStates is idempotent, so both paths are safe.
        if swapPrior then
            BX.try("humanoid.restoreStates", function()
                applyStates(swapPrior)
                log.info("death states restored (dead=%s fallingDown=%s "
                    .. "ragdoll=%s breakJoints=%s) - the character can respawn "
                    .. "normally again",
                    tostring(swapPrior.dead), tostring(swapPrior.fallingDown),
                    tostring(swapPrior.ragdoll), tostring(swapPrior.breakJoints))
            end)
        end

        if not sc then return end
        sc:destroy()
        sc = nil
        log.info("disarmed (%d swaps this session)", stats.swaps)
    end

    return M
end)

--[[ ==== features/antideath.lua ====================================== ]]
-- =============================================================================
-- FEATURES.ANTIDEATH: survive the guard
-- =============================================================================
--
--     local ad = BX.require("features.antideath")
--     ad.arm()      -- hold the character alive
--     ad.disarm()   -- put every original value back
--
-- Ported from V3.1 (LennonHub Proto 61). It does four things, and each one is
-- needed for a different reason:
--
--   * BreakJointsOnDeath = false     the body does not come apart
--   * Dead state disabled            the humanoid cannot enter Dead at all
--   * HealthChanged -> restore       damage that reaches zero is undone
--   * StateChanged -> GettingUp      a Dead transition is turned round
--
-- EVERY ORIGINAL VALUE IS RECORDED AND RESTORED. Leaving BreakJointsOnDeath
-- off and the Dead state disabled is a permanent, obvious modification to the
-- character - it is not something to leave behind when Auto Steal stops.
--
-- WHY THIS IS ARMED FOR THE CYCLE AND NOT ALWAYS.
--
-- In V3.1 this sits behind a flag that defaults to OFF, and that default is
-- why the port died: measured on this client, the character was alive right
-- after the bait hit and then died six seconds into the 3029-stud crossing to
-- the target. The instant teleport removes most of that exposure, but a guard
-- can still land a blow on arrival, so the cycle arms this for its duration
-- and disarms it on the way out.
--
-- RE-ARMED ON RESPAWN. A new character is a new Humanoid with none of these
-- settings, so arming once and walking away protects exactly one life.

BX.module("features.antideath", function(BX)
    local ch  = BX.require("core.character")
    local svc = BX.require("core.services")
    local rs  = BX.require("core.restore")
    local log = BX.require("boot.log").for_module("antideath")

    local M = {}

    local sc = nil
    local saved = nil        -- the values we must put back
    local armedFor = nil     -- which Humanoid the current arming belongs to

    local stats = { arms = 0, deathsBlocked = 0, restores = 0 }
    function M.stats() return table.clone(stats) end

    local function applyTo(char)
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if not hum then return false end
        if armedFor == hum then return true end

        -- Record BEFORE changing anything, and only once per Humanoid.
        saved = {
            humanoid = hum,
            breakJoints = hum.BreakJointsOnDeath,
            deadEnabled = hum:GetStateEnabled(Enum.HumanoidStateType.Dead),
        }
        armedFor = hum

        BX.try("antideath.apply", function()
            rs.remember("antideath.breakJoints",
                function() return hum.BreakJointsOnDeath end,
                function(v) hum.BreakJointsOnDeath = v end)
            rs.remember("antideath.state.Dead",
                function() return hum:GetStateEnabled(Enum.HumanoidStateType.Dead) end,
                function(v) hum:SetStateEnabled(Enum.HumanoidStateType.Dead, v) end)
            hum.BreakJointsOnDeath = false
            hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
        end)

        -- Both handlers go on the scope, so disarming or a re-execute drops
        -- them. V3.1 kept them in a bare table and disconnected by hand.
        sc:connect(hum.HealthChanged, function(hp)
            if hp <= 0 and hum.Parent then
                stats.deathsBlocked = stats.deathsBlocked + 1
                hum.Health = hum.MaxHealth
            end
        end)

        sc:connect(hum.StateChanged, function(_, new)
            if new == Enum.HumanoidStateType.Dead and hum.Parent then
                stats.deathsBlocked = stats.deathsBlocked + 1
                hum:ChangeState(Enum.HumanoidStateType.GettingUp)
                hum.Health = hum.MaxHealth
            end
        end)

        -- ARRIVING AT ZERO HEALTH IS NOT A HEALTH *CHANGE*.
        --
        -- The two handlers above are the only things that revive us, and both
        -- are event-driven - so a character that was ALREADY on 0 health when
        -- anti-death armed never gets revived. With Dead disabled it also
        -- cannot die properly, so it does not respawn either: the player is
        -- left as a zombie on 0 HP indefinitely.
        --
        -- Seen in a 17-minute soak: every cycle logged "hp=0 Running swapped"
        -- and the character never recovered. A humanoid in that state behaves
        -- badly in ways that look like "can't jump" and "my character is
        -- broken" rather than like death.
        --
        -- So check the CURRENT value on arming, not only later changes.
        if hum.Health <= 0 then
            stats.deathsBlocked = stats.deathsBlocked + 1
            log.warn("armed on a humanoid already at 0 health - reviving it")
            hum.Health = hum.MaxHealth
        end

        stats.arms = stats.arms + 1
        log.trace("armed on humanoid (health %.0f/%.0f)", hum.Health, hum.MaxHealth)
        return true
    end

    local function restore()
        local s = saved
        saved, armedFor = nil, nil
        if not s or not s.humanoid or not s.humanoid.Parent then return end
        stats.restores = stats.restores + 1
        BX.try("antideath.restore", function()
            s.humanoid.BreakJointsOnDeath = s.breakJoints
            s.humanoid:SetStateEnabled(Enum.HumanoidStateType.Dead, s.deadEnabled)
        end)
    end

    function M.isArmed() return sc ~= nil end

    function M.arm()
        if sc then return true end
        sc = BX.scope("features.antideath")

        local ok = applyTo(ch.get())

        -- A NEW CHARACTER IS A NEW HUMANOID. Arming once protects one life;
        -- V3.1 re-ran setupAntiDeath from the steal loop for this reason, and
        -- doing it on the respawn signal instead means it cannot be forgotten.
        ch.onSpawn(sc, "antideath.rearm", function(char)
            -- The previous Humanoid is gone with its character, so there is
            -- nothing to restore - just re-apply to the new one.
            saved, armedFor = nil, nil
            applyTo(char)
        end)

        log.info("armed (%s)", ok and "ok" or "no humanoid yet")
        return true
    end

    function M.disarm()
        if not sc then return end
        sc:destroy()
        sc = nil

        -- NEVER HAND BACK A ZOMBIE.
        --
        -- restore() re-enables the Dead state. Doing that while the humanoid
        -- sits on 0 health would kill the player the instant we let go, which
        -- is a worse exit than the one we were preventing. Revive first, then
        -- give the states back.
        BX.try("antideath.reviveOnDisarm", function()
            local hum = ch.humanoid()
            if hum and hum.Parent and hum.Health <= 0 then
                log.warn("disarming on 0 health - reviving before restoring states")
                hum.Health = hum.MaxHealth
            end
        end)

        restore()
        log.info("disarmed (blocked %d deaths this session)", stats.deathsBlocked)
    end

    return M
end)

--[[ ==== features/guard.lua ========================================== ]]
-- =============================================================================
-- FEATURES.GUARD: survive the guard, keep the egg
-- =============================================================================
--
--     local guard = BX.require("features.guard")
--     guard.arm()                  -- anti-hit + anti-ragdoll + drop block
--     guard.disarm()
--     guard.isRagdolled()
--     guard.waitForRecovery(sec)
--     guard.allowDrops(bool)
--
-- These are V3.1's three guard-sequence dependencies. They were armed from the
-- steal loop and from inside primeFirstArea, and the port not having them is
-- why the first full-cycle test died.
--
-- THE GAME RAGDOLLS WITH THE *PHYSICS* STATE, NOT Ragdoll.
--
-- V3.1 learned this the expensive way: an earlier version tested for Ragdoll
-- and FallingDown, which this game NEVER enters, so everything built on it was
-- dead - the ragdoll wait returned instantly and anti-hit never saw a hit to
-- react to. PlatformStand counts too, because Ragdoll.NpcRagdoll sets it.
--
-- WHAT ANTI-DEATH IS NOT. V3.1 ships antiDeathEnabled = false. Anti-death was
-- never its protection against the guard - these three are.

BX.module("features.guard", function(BX)
    local svc = BX.require("core.services")
    local data = BX.require("core.data")
    local ch  = BX.require("core.character")
    local rs  = BX.require("core.restore")
    local log = BX.require("boot.log").for_module("guard")

    local RunService = svc.RunService
    local M = {}

    local K = {
        RISE      = 150,   -- upward studs/s no legal jump can produce
        FLAT_MULT = 2.5,   -- flat speed over WalkSpeed * this is not our doing
        FLAT_MIN  = 150,   -- ...but never react below this, whatever WalkSpeed is
        JOINT_GAP = 0.25,  -- seconds between Motor6D sweeps; they are not free
        HOLD_MAX  = 2.75,  -- longest we wait out the server knockdown
        HOLD_GRACE = 0.25, -- covers the release round trip
    }
    M.K = K

    local sc = nil
    local stats = { launchesCancelled = 0, standUps = 0, dropsRefused = 0 }
    function M.stats() return table.clone(stats) end

    ---------- ragdoll state ----------

    function M.isRagdolled()
        local hum = ch.humanoid()
        if not hum then return false end
        if hum.PlatformStand then return true end
        local s = hum:GetState()
        return s == Enum.HumanoidStateType.Physics
            or s == Enum.HumanoidStateType.Ragdoll
            or s == Enum.HumanoidStateType.FallingDown
    end

    function M.waitForRecovery(seconds)
        local deadline = os.clock() + (seconds or 4)
        while os.clock() < deadline do
            if not M.isRagdolled() then return true end
            RunService.Heartbeat:Wait()
        end
        return false
    end

    ---------- anti-ragdoll ----------

    -- Re-applied on respawn: a new character is a new Humanoid with none of
    -- this set, so applying once protects exactly one life.
    local function applyAntiRagdoll(char)
        char = char or ch.get()
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if not hum then return false end
        BX.try("guard.antiRagdoll", function()
            -- These were disabled and never put back, so a character stayed
            -- unable to ragdoll or fall for the rest of the session.
            rs.remember("guard.state.Ragdoll",
                function() return hum:GetStateEnabled(Enum.HumanoidStateType.Ragdoll) end,
                function(v) hum:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, v) end)
            rs.remember("guard.state.FallingDown",
                function() return hum:GetStateEnabled(Enum.HumanoidStateType.FallingDown) end,
                function(v) hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, v) end)
            hum:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
            hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
            for _, d in ipairs(char:GetDescendants()) do
                if d:IsA("Motor6D") then d.Enabled = true end
            end
        end)
        return true
    end

    ---------- drop block ----------

    -- The guard's hit makes the client drop what it is carrying.
    --
    -- RESTORED ON DISARM. V3.1 patched EggState.DropFieldEgg and never put it
    -- back, so a wrapped DropFieldEgg stayed installed for the rest of the
    -- session even with Auto Steal switched off.
    local dropOriginal, dropInstalled, eggStateRef = nil, false, nil
    local dropAllowed = false

    local function installDropBlock()
        if dropInstalled then return true end
        eggStateRef = eggStateRef or data.eggState()
        if not eggStateRef or type(eggStateRef.DropFieldEgg) ~= "function" then
            log.warn("cannot block egg drops - EggState.DropFieldEgg missing")
            return false
        end

        dropOriginal = eggStateRef.DropFieldEgg
        eggStateRef.DropFieldEgg = function(reason, ...)
            if not dropAllowed then
                stats.dropsRefused = stats.dropsRefused + 1
                log.trace("drop refused: %s", tostring(reason))
                return
            end
            return dropOriginal(reason, ...)
        end
        dropInstalled = true
        log.info("egg-drop block installed")
        return true
    end

    local function removeDropBlock()
        if not dropInstalled then return end
        BX.try("guard.restoreDrop", function()
            if eggStateRef and dropOriginal then
                eggStateRef.DropFieldEgg = dropOriginal
            end
        end)
        dropInstalled, dropOriginal = false, nil
    end

    -- For a drop we actually want, such as handing the bait egg over.
    function M.allowDrops(on) dropAllowed = on and true or false end

    ---------- anti-hit ----------

    local blocked, ups, jointAt = 0, 0, 0

    local function antiHitStep()
        local hum, hrp = ch.humanoid(), ch.root()
        if not hum or not hrp then return end

        local st = hum:GetState()
        -- A jump we made ourselves is not a launch.
        if st == Enum.HumanoidStateType.Jumping then return end

        -- CANCEL THE LAUNCH. The ragdoll payload carries an impulse - V3.1
        -- measured one at Y = +791 - and the server applies it. Only the part
        -- we cannot account for is removed: zeroing everything froze ordinary
        -- walking as well as the fling.
        local v = hrp.AssemblyLinearVelocity
        local flat = (v * Vector3.new(1, 0, 1)).Magnitude
        local flatCap = math.max((hum.WalkSpeed or 16) * K.FLAT_MULT, K.FLAT_MIN)
        if v.Y > K.RISE or flat > flatCap then
            local keep = Vector3.zero
            if flat > 0.001 then
                keep = (v * Vector3.new(1, 0, 1)).Unit * math.min(flat, hum.WalkSpeed or 16)
            end
            hrp.AssemblyLinearVelocity = Vector3.new(keep.X, math.min(v.Y, 0), keep.Z)
            hrp.AssemblyAngularVelocity = Vector3.zero
            blocked = blocked + 1
            stats.launchesCancelled = blocked
        end

        -- STAND BACK UP. Physics is the state this game uses.
        if hum.PlatformStand or hum.Sit
           or st == Enum.HumanoidStateType.Physics
           or st == Enum.HumanoidStateType.Ragdoll
           or st == Enum.HumanoidStateType.FallingDown
           or st == Enum.HumanoidStateType.PlatformStanding then
            pcall(function()
                hum.PlatformStand = false
                hum.Sit = false
                hum:ChangeState(Enum.HumanoidStateType.GettingUp)
            end)
            ups = ups + 1
            stats.standUps = ups

            -- A ragdoll unbinds the rig's joints; without re-enabling them the
            -- character stays a heap on the floor whatever state we ask for.
            -- Rate-limited, because a full descendants walk is not free.
            local now = os.clock()
            if now - jointAt > K.JOINT_GAP then
                jointAt = now
                local char = ch.get()
                if char then
                    for _, d in ipairs(char:GetDescendants()) do
                        if d:IsA("Motor6D") and not d.Enabled then d.Enabled = true end
                    end
                end
            end
        end
    end

    -- HOW LONG THE SERVER STILL CONSIDERS US KNOCKED DOWN.
    --
    -- This is the missing piece that made the instant steal fail. The server
    -- refuses CarryFieldEgg outright while its own RagdollEndTime is in the
    -- future, and says so: "Cannot carry eggs while downed". It publishes that
    -- as an attribute on the player, on the SERVER clock:
    --     u4:SetAttribute("RagdollEndTime", workspace:GetServerTimeNow() + dur)
    --
    -- V3.1 notes this is a no-op on a normal tweened steal, because the trip is
    -- long enough to outlast the ~2.5s knockdown. With an instant teleport we
    -- arrive INSIDE that window every single time, so it stops being an edge
    -- case and becomes the thing that decides the cycle.
    function M.ragdollRemaining()
        local left = 0
        BX.try("guard.ragdollRemaining", function()
            local plr = svc.LocalPlayer
            local t = plr and plr:GetAttribute("RagdollEndTime")
            if type(t) == "number" then
                left = math.max(left, t - workspace:GetServerTimeNow())
            end
        end)
        return math.max(0, left)
    end

    -- Wait it out, bounded. Returns how long we actually waited.
    --
    -- THE COUNTDOWN ENDS BEFORE THE SERVER DOES. ragdollRemaining reaches zero
    -- the instant the deadline passes, but the server still has to process the
    -- release and send it. V3.1 measured that gap costing an egg:
    --     17.37 fired prompt -> true      <- we thought we were free
    --     17.43 ragdoll END after 2.4s    <- server released 60ms later
    -- so a short grace covers the round trip.
    function M.waitForServerRelease(cancel)
        local held = M.ragdollRemaining()
        if held <= 0 then return 0 end

        local t0 = os.clock()
        local deadline = os.clock() + math.min(held, K.HOLD_MAX)
        while os.clock() < deadline do
            if cancel and cancel() then break end
            task.wait(0.05)
            if M.ragdollRemaining() <= 0 then break end
        end
        task.wait(K.HOLD_GRACE)
        local waited = os.clock() - t0
        log.trace("server held us %.2fs - waited %.2fs", held, waited)
        return waited
    end

    ---------- lifecycle ----------

    function M.isArmed() return sc ~= nil end

    function M.arm()
        if sc then return true end
        sc = BX.scope("features.guard")
        blocked, ups, jointAt = 0, 0, 0
        dropAllowed = false

        applyAntiRagdoll()
        installDropBlock()

        -- Registered through onFrame so its per-frame cost is profiled by name
        -- rather than hiding inside "Auto Steal".
        sc:onFrame("antihit", RunService.Heartbeat, antiHitStep)

        ch.onSpawn(sc, "guard.respawn", function(char)
            applyAntiRagdoll(char)
        end)

        log.info("armed (anti-hit + anti-ragdoll + drop block)")
        return true
    end

    function M.disarm()
        if not sc then return end
        sc:destroy()
        sc = nil
        dropAllowed = true
        removeDropBlock()
        log.info("disarmed (%d launches cancelled, %d stand-ups, %d drops refused)",
            stats.launchesCancelled, stats.standUps, stats.dropsRefused)
    end

    return M
end)

--[[ ==== features/plot.lua =========================================== ]]
-- =============================================================================
-- FEATURES.PLOT: where home is, and when the egg is actually ours
-- =============================================================================
--
--     local plot = BX.require("features.plot")
--
--     plot.home()          -- Vector3 to carry to, or nil with a reason
--     plot.claimedSince(t) -- has the server claimed an egg since t?
--     plot.onClaim(sc, label, fn)
--
-- THE HARDCODED FALLBACK IS DELIBERATELY NOT PORTED.
--
-- V3.1 ended its resolution ladder with
--     local SAFE_POS_FALLBACK = Vector3.new(512, 68, -362)
-- which is a literal measured on ONE account. Its own comment says what that
-- cost: when PlotState.FindRespawnCFrame returns nothing on somebody else's
-- client - and it does, it is the first thing to resolve after a join and the
-- last to recover after a hop - the carry was flown to that account's plot
-- instead of theirs. The trip works perfectly, the egg is never voided, and
-- the claim simply never fires because they are standing on a stranger's base.
--
-- That is the mobile report exactly: the egg survives four thousand studs
-- through every zone and dies at the destination with "Delivery failed! The
-- egg was returned to its nest". Nothing about the journey was wrong. The
-- address was.
--
-- So the ladder ends in nil, and a nil home REFUSES the delivery rather than
-- flying somewhere plausible. Failing to start is recoverable; carrying an egg
-- to a stranger's plot is not.

BX.module("features.plot", function(BX)
    local svc = BX.require("core.services")
    local data = BX.require("core.data")
    local log = BX.require("boot.log").for_module("plot")

    local M = {}

    local K = {
        HOME_TTL = 30,      -- slots can change while connected, so re-resolve
        ARRIVE   = 18,      -- close enough to the plot middle
    }
    M.K = K

    -- THROUGH core.data. A private FindFirstChild + require here bypassed
    -- the one place that knows whether this executor can require game
    -- modules at all, and cached a miss forever. core.data probes once,
    -- lets a miss expire, and backs off - see core/data.lua.
    local PlotState = data.plotState()

    local cached, cachedAt, cachedVia = nil, 0, nil

    -- Every rung is about THIS player: the game's own respawn point, their
    -- plot model, then this server's spawn. None of them is a literal.
    local function resolve()
        local pos, via

        if PlotState then
            BX.try("plot.findRespawn", function()
                local cf = PlotState.FindRespawnCFrame and PlotState.FindRespawnCFrame()
                if typeof(cf) == "CFrame" then pos, via = cf.Position, "PlotState.FindRespawnCFrame" end
            end)
        end

        if not pos and PlotState then
            BX.try("plot.resolveSlot", function()
                local slot = PlotState.ResolveLocalSlot and PlotState.ResolveLocalSlot()
                local plots = slot and workspace:FindFirstChild("Plots")
                local mine = plots and plots:FindFirstChild(tostring(slot))
                if mine then
                    local cf = mine:GetPivot()
                    if typeof(cf) == "CFrame" then pos, via = cf.Position, "plot " .. tostring(slot) end
                end
            end)
        end

        if not pos then
            BX.try("plot.spawnLocation", function()
                local sl = workspace:FindFirstChildOfClass("SpawnLocation")
                if sl and sl:IsA("BasePart") then
                    pos, via = sl.Position + Vector3.new(0, 4, 0), "SpawnLocation"
                end
            end)
        end

        if not pos then
            BX.try("plot.spawnTarget", function()
                local st = workspace:FindFirstChild("SpawnTarget", true)
                if st and st:IsA("BasePart") then
                    pos, via = st.Position + Vector3.new(0, 4, 0), "SpawnTarget"
                end
            end)
        end

        return pos, via
    end

    -- Returns pos, via  -- or nil, reason
    function M.home()
        local now = os.clock()
        if cached and (now - cachedAt) < K.HOME_TTL then
            return cached, cachedVia
        end
        local pos, via = resolve()
        if not pos then
            -- Loud, and it stops the cycle. See the header.
            log.error("cannot resolve this player's plot - refusing to deliver "
                .. "(PlotState=%s)", tostring(PlotState ~= nil))
            return nil, "no plot resolved"
        end
        if via ~= cachedVia then
            log.info("home resolved via %s at %s", via, tostring(pos))
        end
        cached, cachedAt, cachedVia = pos, now, via
        return cached, cachedVia
    end

    function M.forget()
        cached, cachedAt = nil, 0
    end


    ---------- the safe zone: where a carried egg is delivered ----------

    -- NOT THE PLOT. V3.1 BX.safeZonePos, resolved in this order:
    --     1. workspace SpawnLocation   + 4 studs up
    --     2. workspace SpawnTarget     + 4 studs up
    --     3. the plot                  (fallback only)
    --
    -- An earlier version of this port carried to the plot centre because
    -- delivery eventually involves your own area. That is not where V3.1 goes
    -- and not where the claim happens - the egg has to reach the safe zone.
    --
    -- The hardcoded Vector3 that ends V3.1 ladder is deliberately still not
    -- ported; see the header.
    local szCache, szAt, szVia = nil, 0, nil

    function M.safeZone()
        local now = os.clock()
        if szCache and (now - szAt) < K.HOME_TTL then
            return szCache, szVia
        end

        local pos, via

        BX.try("plot.spawnLocationZone", function()
            local sl = workspace:FindFirstChildOfClass("SpawnLocation")
            if sl and sl:IsA("BasePart") then
                pos, via = sl.Position + Vector3.new(0, 4, 0), "SpawnLocation"
            end
        end)

        if not pos then
            BX.try("plot.spawnTargetZone", function()
                local st = workspace:FindFirstChild("SpawnTarget", true)
                if st and st:IsA("BasePart") then
                    pos, via = st.Position + Vector3.new(0, 4, 0), "SpawnTarget"
                end
            end)
        end

        if not pos then
            -- Fallback only, and it is the players OWN plot - resolved
            -- dynamically, never a literal.
            local p, pvia = M.home()
            if p then pos, via = p, "plot fallback (" .. tostring(pvia) .. ")" end
        end

        if not pos then
            log.error("cannot resolve a safe zone - refusing to deliver")
            return nil, "unresolved"
        end

        if via ~= szVia then
            log.info("safe zone resolved via %s at %s", via, tostring(pos))
        end
        szCache, szAt, szVia = pos, now, via
        return szCache, szVia
    end

    function M.forgetSafeZone()
        szCache, szAt = nil, 0
    end

    ---------- claim ----------

    -- The server telling us the egg is ours. This is the ONLY authority on a
    -- successful delivery - arriving at the plot is not delivery, and V3.1's
    -- own logs are full of perfect arrivals that were never claimed.
    local lastClaimAt, lastClaimName = 0, nil
    local listeners = {}

    function M.claimedSince(t)
        return lastClaimAt > (t or 0), lastClaimName
    end

    function M.onClaim(sc, label, fn)
        listeners[#listeners + 1] = { scope = sc, label = label, fn = fn }
    end

    local sc = BX.scope("features.plot")
    local EggState
    BX.try("plot.resolveEggState", function()
        local found = svc.ReplicatedStorage:FindFirstChild("EggState", true)
        if found and found:IsA("ModuleScript") then EggState = require(found) end
    end)

    if EggState and EggState.FieldClaimed then
        BX.try("plot.armClaimWatch", function()
            sc:connect(EggState.FieldClaimed, function(info)
                lastClaimAt = os.clock()
                lastClaimName = (type(info) == "table"
                    and (info.DisplayName or info.AssetCategory)) or "egg"
                log.info("CLAIM: server claimed our egg -> %s", tostring(lastClaimName))
                for i = #listeners, 1, -1 do
                    local L = listeners[i]
                    if not L.scope or L.scope.dead then
                        table.remove(listeners, i)
                    else
                        BX.try("plot/" .. L.label, L.fn, lastClaimName)
                    end
                end
            end)
        end)
    else
        log.warn("EggState.FieldClaimed unavailable - deliveries cannot be confirmed")
    end

    M._listeners = function() return #listeners end
    return M
end)

--[[ ==== features/regrab.lua ========================================= ]]
-- =============================================================================
-- FEATURES.REGRAB: go back for an egg the guard knocked out of our hands
-- =============================================================================
--
--     local regrab = BX.require("features.regrab")
--     local ok, info = regrab.recover(uid, { cancel = fn })
--
-- THE RECOVERY IS NOT A BAIT RESTART.
--
-- This is V3.1 BX.regrabEgg, and the important part is what it does NOT do: it
-- never goes back to the Forest guard to run the bait cycle again. It goes back
-- for THE EGG. Restarting at the guard means a full cross-map trip, a second
-- knockdown and a fresh prime, to end up chasing the same egg anyway.
--
-- CHASE IT, DO NOT AIM AT WHERE IT WAS.
--
-- V3.1 note, and it is the whole reason this re-reads the record on every
-- attempt: a dropped egg does not stay put. A guard picks it up and walks it
-- back to its nest, so a position read once before travelling is wrong by the
-- time we arrive and the carry is refused for range forever. That is "it goes
-- for the dropped egg and never picks it up".
--
-- WAIT FOR THREE THINGS BEFORE ASKING, IN THIS ORDER
--
--   1. The settle. The drop and the ragdoll arrive a frame apart (V3.1
--      measured the drop at 14.44 and BeginRagdoll at 14.50), so checking in
--      between sees a character that is not down yet and waits for nothing.
--   2. The server knockdown. "Cannot carry eggs while downed" is its own
--      refusal text, so there is no point asking until RagdollEndTime passes.
--   3. The egg itself. Straight after a drop the record is stale, and while a
--      guard is walking it back it reads GuardCarried. Claimed means it
--      actually banked; a missing record means it is gone.
--
-- BOUNDED, ALWAYS. Two recoveries per steal (V3.1 midCarryRegrabs < 2) and
-- REGRAB_TRIES attempts inside each, so a repeatedly-dropped egg cannot turn
-- into a fast restart loop. The budget is reset by the caller on a fresh steal.

BX.module("features.regrab", function(BX)
    local svc     = BX.require("core.services")
    local eggs    = BX.require("features.eggs")
    local instant = BX.require("features.instant")
    local guard   = BX.require("features.guard")
    local ch      = BX.require("core.character")
    local dev     = BX.require("core.device")
    local log     = BX.require("boot.log").for_module("regrab")

    local RunService = svc.RunService
    local M = {}

    local K = {
        SETTLE      = 0.08,   -- V3.1 K.REGRAB_SETTLE
        WAIT        = 8.0,    -- K.REGRAB_WAIT - how long to wait for it to settle
        POLL        = 0.05,
        TRIES       = 4,      -- K.REGRAB_TRIES
        MAX_PER_STEAL = 2,    -- V3.1 midCarryRegrabs < 2
    }
    M.K = K

    local stats = { runs = 0, recovered = 0, banked = 0, gone = 0, failed = 0, cancelled = 0 }
    function M.stats() return table.clone(stats) end

    -- Takeable again? Returns pos, state.
    local function settledPos(uid)
        local r = eggs.get(uid)
        if not r then return nil, nil end
        return r.pos, r.state
    end

    -- uid    the egg we just lost
    -- opts   { cancel = fn, attempt = n }  attempt is the caller budget counter
    function M.recover(uid, opts)
        opts = opts or {}
        local cancel = opts.cancel or function() return false end
        stats.runs = stats.runs + 1
        local t0 = os.clock()

        -- 1. Let the knockdown register before testing for it.
        task.wait(K.SETTLE)
        if cancel() then
            stats.cancelled = stats.cancelled + 1
            return false, { reason = "cancelled", recovery = "cancelled" }
        end

        -- 2. The server hold. Asking inside it only collects refusals.
        local heldFor = guard.waitForServerRelease(cancel)
        if cancel() then
            stats.cancelled = stats.cancelled + 1
            return false, { reason = "cancelled", recovery = "cancelled" }
        end

        -- 3. Wait for the egg to be takeable again.
        local deadline = os.clock() + dev.scale(K.WAIT)
        local pos, state, said
        repeat
            if cancel() then
                stats.cancelled = stats.cancelled + 1
                return false, { reason = "cancelled", recovery = "cancelled" }
            end
            pos, state = settledPos(uid)
            if state == "Claimed" then
                -- Not a failure: it banked. Somebody got it, possibly us.
                stats.banked = stats.banked + 1
                log.info("drop_recovery=banked uid=%s (the egg was claimed)", tostring(uid))
                return false, { reason = "claimed", recovery = "banked" }
            end
            if state == nil then
                stats.gone = stats.gone + 1
                log.warn("drop_recovery=failed uid=%s (record gone)", tostring(uid))
                return false, { reason = "gone", recovery = "failed" }
            end
            if state == "Slot" or state == "Dropped" then break end
            if state ~= said then
                said = state
                log.trace("egg is %s - waiting for it to settle", tostring(state))
            end
            task.wait(K.POLL)
        until os.clock() > deadline

        if state ~= "Slot" and state ~= "Dropped" then
            stats.failed = stats.failed + 1
            log.warn("drop_recovery=failed uid=%s (still %s after %.1fs)",
                tostring(uid), tostring(state), os.clock() - t0)
            return false, { reason = "never settled (" .. tostring(state) .. ")",
                            recovery = "failed" }
        end

        -- 4. Go and get it. INSTANT, not a cross-map tween.
        --
        -- instant.take teleports onto the egg, holds position against the
        -- server relocate and races CarryFieldEgg - the same path that already
        -- works for the outbound target. A tween back is what gets stuck in
        -- snapbacks, and it is slower into the bargain.
        for attempt = 1, K.TRIES do
            if cancel() then
                stats.cancelled = stats.cancelled + 1
                return false, { reason = "cancelled", recovery = "cancelled" }
            end

            -- RE-READ EVERY ATTEMPT. The guard is walking it back.
            local pNow, sNow = settledPos(uid)
            if sNow == "Claimed" then
                stats.banked = stats.banked + 1
                log.info("drop_recovery=banked uid=%s (claimed on the way)", tostring(uid))
                return false, { reason = "claimed", recovery = "banked" }
            end
            if not pNow then
                stats.gone = stats.gone + 1
                log.warn("drop_recovery=failed uid=%s (record gone on the way)", tostring(uid))
                return false, { reason = "gone", recovery = "failed" }
            end

            local hrp = ch.root()
            local gapBefore = hrp and (pNow - hrp.Position).Magnitude or -1

            local got, info = instant.take(uid, pNow, {
                cancel = cancel,
                areaId = opts.areaId, nestId = opts.nestId,
            })

            if got then
                stats.recovered = stats.recovered + 1
                log.info("drop_recovery=tp uid=%s attempt %d/%d in %.2fs "
                    .. "(was %.0f studs out, %d calls)",
                    tostring(uid), attempt, K.TRIES, os.clock() - t0,
                    gapBefore, info and info.calls or -1)
                return true, { recovery = "tp", attempts = attempt,
                               ms = (os.clock() - t0) * 1000 }
            end

            -- THE TELEPORT ITSELF WAS REFUSED, which is a different failure
            -- from the egg being unavailable - and it is the one that used to
            -- disappear into a long tween.
            if info and info.pulledBack then
                log.warn("drop_recovery=tp_refused uid=%s attempt %d/%d "
                    .. "(landed %.0f studs off, reason=%s)",
                    tostring(uid), attempt, K.TRIES,
                    info.gap or -1, tostring(info.reason))
            else
                log.trace("attempt %d/%d: %s (egg %s, %.0f studs)",
                    attempt, K.TRIES, tostring(info and info.reason),
                    tostring(sNow), gapBefore)
            end

            -- Never round again without yielding.
            task.wait(dev.scale(K.POLL))
        end

        stats.failed = stats.failed + 1
        log.warn("drop_recovery=failed uid=%s after %d attempts in %.2fs",
            tostring(uid), K.TRIES, os.clock() - t0)
        return false, { reason = "no regrab", recovery = "failed" }
    end

    return M
end)

--[[ ==== features/carry.lua ========================================== ]]
-- =============================================================================
-- FEATURES.CARRY: get the egg to the safe zone and get it claimed
-- =============================================================================
--
--     local carry = BX.require("features.carry")
--     local ok, info = carry.home(uid, { cancel = fn })
--
-- THE DESTINATION IS THE SAFE ZONE, NOT THE PLOT.
--
-- V3.1 line 9487:  local DELIVER_POS = SAFE_POS
--                  local okD, dp, how = pcall(BX.safeZonePos)
--                  if okD and typeof(dp) == "Vector3" then DELIVER_POS = dp end
--
-- and BX.safeZonePos resolves, in this order:
--     1. workspace SpawnLocation      + 4 studs up
--     2. workspace SpawnTarget        + 4 studs up
--     3. the plot                     (fallback only)
--
-- An earlier version of this module carried to the plot centre, because
-- delivery eventually involves your own area. That is not where V3.1 goes and
-- it is not where the claim happens.
--
-- The hardcoded Vector3 that ends V3.1's ladder is still NOT ported - see
-- features/plot.lua for why (it was one account's coordinates, and everyone
-- else inherited them).
--
-- THE ROUTE IS ONE ARC, NOT A CLIMB AND A CROSSING.
--
-- V3.1 line 9590:
--     BX.arcTweenTo(DELIVER_POS, carrySpeed, "carry home", 5,
--         function() return heldEggUid == nil end)
--     if heldEggUid == nil then droppedMidTween = true
--     else BX.arcDescend("deliver") end
--
-- One call. The height is the arc mover's own K.ARC_CRUISE_UP - 18 studs above
-- the higher end - and nothing else. An earlier version here lifted 120 studs
-- straight up first and then crossed, which is a route V3.1 never flies.
--
-- THE CANCEL PREDICATE IS THE DROPPED-EGG HANDLING.
--
-- `heldEggUid == nil` is passed INTO the mover, so the moment the egg leaves
-- our hands the leg stops where it is. Without it we fly the remaining three
-- thousand studs empty-handed and only notice on arrival - which is what
-- "lost the egg in transit" looked like, several seconds after the fact.
--
-- WHY THE CARRY LEG IS NOT CLAMPED TO THE ANTICHEAT ALLOWANCE. V3.1 tried it
-- and reverted it: the carry runs unspoofed at ~499 while the allowance is
-- ~230, and Titan at 4293 studs is where the snap-back happens. But holding the
-- carry at 230 hands the guard the trip - chase speeds are base * 4, so
-- Prehistoric 608, Cosmic 800, Cherry Blossom 888, Titan 918. At 230 they all
-- catch you, everywhere.
--
-- ARRIVING IS NOT DELIVERING. The only authority is the server's FieldClaimed
-- signal. V3.1's logs are full of perfect arrivals that were never claimed.

BX.module("features.carry", function(BX)
    local svc  = BX.require("core.services")
    local move = BX.require("features.movement")
    local plot = BX.require("features.plot")
    local eggs = BX.require("features.eggs")
    local ch   = BX.require("core.character")
    local dev  = BX.require("core.device")
    local log  = BX.require("boot.log").for_module("carry")

    local M = {}

    local K = {
        SPEED      = 500,   -- V3.1 K.CARRY_FLOOR; above it the server voids the egg
        ARRIVE     = 5,     -- V3.1 passes arrive = 5 to the carry arc
        CLAIM_WAIT = 6,     -- how long to wait for the server to claim it
    }
    M.K = K

    local stats = { runs = 0, delivered = 0, failed = 0, cancelled = 0, lost = 0 }
    function M.stats() return table.clone(stats) end

    -- Cheap, and the reason a lost egg is noticed at a stage boundary rather
    -- than after a four-thousand-stud flight.
    local function holding(uid)
        local r = eggs.get(uid)
        if not r then return false, "gone" end
        return r.state == "Carried", r.state
    end

    function M.home(uid, opts)
        opts = opts or {}
        local outerCancel = opts.cancel
        stats.runs = stats.runs + 1

        local t0 = os.clock()
        local stages = {}
        local function stage(name, fn)
            local s0 = os.clock()
            local ok, info = fn()
            stages[#stages + 1] = {
                name = name, ms = (os.clock() - s0) * 1000, ok = ok and true or false,
            }
            return ok, info
        end

        local function report()
            local parts = {}
            for _, s in ipairs(stages) do
                parts[#parts + 1] = ("%s=%.0fms%s"):format(s.name, s.ms, s.ok and "" or "!")
            end
            return table.concat(parts, " ")
        end

        local function fail(why)
            stats.failed = stats.failed + 1
            log.warn("FAILED %s uid=%s after %.2fs [%s] tier=%s",
                why, tostring(uid), os.clock() - t0, report(), dev.tier)
            return false, { reason = why, stages = stages, elapsed = os.clock() - t0 }
        end

        local dest, via = plot.safeZone()
        if not dest then return fail("no safe zone resolved") end

        if not ch.root() then return fail("no character") end

        -- THE CANCEL THE MOVER ACTUALLY GETS.
        --
        -- Two reasons to stop: the caller asked, or the egg is no longer ours.
        -- The second is V3.1's `function() return heldEggUid == nil end`, and it
        -- is checked against the game rather than a local flag so a server-side
        -- drop is seen the same frame.
        --
        -- The egg check is rate-limited: this predicate is polled every frame
        -- by the mover, and eggs.get is a module call per invocation. Four
        -- times a second is fast enough to stop a leg and cheap enough not to
        -- matter.
        local lastCheck, lastHeld = 0, true
        local function carryCancel()
            if outerCancel and outerCancel() then return true end
            local now = os.clock()
            if (now - lastCheck) >= 0.25 then
                lastCheck = now
                lastHeld = holding(uid)
            end
            return not lastHeld
        end

        local before = ch.root().Position
        local distance = (Vector3.new(dest.X, 0, dest.Z)
            - Vector3.new(before.X, 0, before.Z)).Magnitude

        log.info("carrying %s to the safe zone via %s (%.0f studs, tier=%s)",
            tostring(uid), tostring(via), distance, dev.tier)

        -- ONE ARC. The climb is the mover's own 18 studs.
        local arrived, moveInfo = stage("arc", function()
            return move.travel{
                to = dest, speed = K.SPEED, arrive = K.ARRIVE,
                carrying = true, cancel = carryCancel, tag = "carry home",
            }
        end)

        -- Dropped mid-route: the mover stopped where it was, which is the point.
        local stillOurs, state = holding(uid)
        if not stillOurs then
            stats.lost = stats.lost + 1
            local gone = ch.root()
            local travelled = gone and (gone.Position - before).Magnitude or -1
            -- Where it happened, not just that it did.
            log.warn("carry ended mid-route: egg is %s after %.0f/%.0f studs (%.2fs)",
                tostring(state), travelled, distance, os.clock() - t0)
            return false, {
                reason = "dropped in transit (" .. tostring(state) .. ")",
                stages = stages, droppedAt = travelled, distance = distance,
            }
        end

        if outerCancel and outerCancel() then
            stats.cancelled = stats.cancelled + 1
            return false, { reason = "cancelled", stages = stages }
        end
        if not arrived then
            return fail("could not reach the safe zone ("
                .. tostring(moveInfo and moveInfo.reason) .. ")")
        end

        -- Then down, exactly as V3.1 does after the arc.
        stage("descend", function()
            return move.descend("deliver"), nil
        end)

        -- ARRIVING IS NOT DELIVERING.
        local claimFrom = os.clock()
        local claimed = stage("claim", function()
            local until_ = os.clock() + dev.scale(K.CLAIM_WAIT)
            repeat
                if outerCancel and outerCancel() then return false, { reason = "cancelled" } end
                local got = plot.claimedSince(claimFrom)
                if got then return true, { reason = "claimed" } end
                svc.RunService.Heartbeat:Wait()
            until os.clock() > until_
            return false, { reason = "no claim" }
        end)

        if not claimed then
            -- Distinguish the two: an egg still in hand that was never claimed
            -- is a different problem from one taken off us at the door.
            local have, st = holding(uid)
            return fail(have and "arrived but never claimed"
                or ("lost at the door (" .. tostring(st) .. ")"))
        end

        stats.delivered = stats.delivered + 1
        log.info("DELIVERED uid=%s in %.2fs via %s [%s] tier=%s",
            tostring(uid), os.clock() - t0, tostring(via), report(), dev.tier)
        return true, { reason = "delivered", stages = stages, elapsed = os.clock() - t0 }
    end

    return M
end)

--[[ ==== features/bait.lua =========================================== ]]
-- =============================================================================
-- FEATURES.BAIT: the Forest prime - pick one up, let the guard take it
-- =============================================================================
--
--     local bait = BX.require("features.bait")
--     local ok, info = bait.prime({ cancel = fn })
--
-- WHY THIS STAGE EXISTS AT ALL.
--
-- Dropping an egg by hand is not the same event as losing one to a guard, and
-- that difference is what decides whether the REAL steal delivers. The prime
-- takes a throwaway egg in the first area, stands in that guard's reach, and
-- lets the hit land - which puts the guard/knockdown sequence into the state
-- the real steal needs.
--
-- THE FIRST AREA, DELIBERATELY. From the game's own guard directory:
--     Forest  WalkSpeed 16  HitDistance 2.5  FlatRadius 20
-- It is the slowest guard in the game and it sits next to the safe zone, so
-- taking a hit here costs nothing. Taking one at Titan Temple (WalkSpeed 229.5,
-- HitDistance 7) is what loses the real egg.
--
-- The area is resolved from the map - leftmost guard area by bounds - so a
-- renamed or reordered area cannot break it.

BX.module("features.bait", function(BX)
    local svc  = BX.require("core.services")
    local data = BX.require("core.data")
    local move = BX.require("features.movement")
    local ch   = BX.require("core.character")
    local dev  = BX.require("core.device")
    local log  = BX.require("boot.log").for_module("bait")

    local RunService = svc.RunService
    local M = {}

    local K = {
        AREA_WAIT    = 5,     -- how long to wait for GuardAreas to stream in
        APPROACH     = 1200,  -- speed to the bait egg
        ARRIVE       = 4,
        PICKUP_WAIT  = 3,     -- how long to keep asking for the carry
        REHOPS       = 2,     -- server pull-backs we will answer before giving up
        HIT_WAIT     = 4.0,   -- how long to stand in reach waiting for the hit
        WITNESS_HOLD = 0.35,  -- stay anchored this long after a witness fires
    }
    M.K = K

    -- THROUGH core.data. A private FindFirstChild + require here bypassed
    -- the one place that knows whether this executor can require game
    -- modules at all, and cached a miss forever. core.data probes once,
    -- lets a miss expire, and backs off - see core/data.lua.
    local EggState, SlotIdentity = data.eggState(), data.slotIdentity()

    ---------- the first area ----------

    local areaCached = nil

    -- WHY THE FOREST STEP WAS SKIPPED ON A FRESH JOIN.
    --
    -- This reads Workspace.__OBJECTS.Areas.GuardAreas, which streams in a
    -- moment after spawn. Turn Auto Steal on before that lands and it returns
    -- nil - and V3.1's caller treated nil as "no bait" silently, so the Forest
    -- prime was simply skipped with nothing said. Waiting is cheap and happens
    -- at most once, because the answer is cached for the session.
    function M.firstAreaId(waitFor)
        if areaCached then return areaCached end

        if waitFor then
            local deadline = os.clock() + waitFor
            while os.clock() < deadline do
                local there = false
                pcall(function()
                    there = workspace.__OBJECTS.Areas.GuardAreas:GetChildren()[1] ~= nil
                end)
                if there then break end
                task.wait(0.2)
            end
        end

        local best, bestX
        BX.try("bait.resolveArea", function()
            for _, a in ipairs(workspace.__OBJECTS.Areas.GuardAreas:GetChildren()) do
                local b = a:FindFirstChild("Bounds")
                if b and b:IsA("BasePart") then
                    local x = b.Position.X - b.Size.X * 0.5
                    if not best or x < bestX then best, bestX = a.Name, x end
                end
            end
        end)
        if best then
            areaCached = best
            log.info("first area resolved: %s (leftmost at x=%.0f)", best, bestX)
        else
            log.warn("guard areas have not streamed in - no bait area")
        end
        return areaCached
    end

    local function findGuard(areaId)
        if not areaId then return nil end
        local live = workspace:FindFirstChild("_Guards")
        if live then
            for _, g in ipairs(live:GetChildren()) do
                if g.Name == areaId or g:GetAttribute("AreaId") == areaId then return g end
            end
        end
        local a
        pcall(function() a = workspace.__OBJECTS.Areas.GuardAreas[areaId] end)
        return a and a:FindFirstChild("Guard") or nil
    end

    local function guardPart(guard)
        if not guard then return nil end
        local root = guard:FindFirstChild("HumanoidRootPart")
            or guard:FindFirstChild("Collider")
            or guard:FindFirstChild("Head")
        if root and root:IsA("BasePart") then return root end
        local best
        for _, d in ipairs(guard:GetDescendants()) do
            if d:IsA("BasePart") then
                local v = d.Size.X * d.Size.Y * d.Size.Z
                if not best or v > best.v then best = { p = d, v = v } end
            end
        end
        return best and best.p or nil
    end

    ---------- the prime ----------

    local stats = { runs = 0, hits = 0, noEgg = 0, noPickup = 0, noHit = 0, cancelled = 0 }
    function M.stats() return table.clone(stats) end

    function M.prime(opts)
        opts = opts or {}
        local cancel = opts.cancel
        stats.runs = stats.runs + 1
        local t0 = os.clock()

        local areaId = M.firstAreaId(K.AREA_WAIT)
        if not areaId then
            return false, { reason = "no bait area" }
        end
        -- Late arrival: re-ask core.data (cheap, rate-limited) rather than
        -- carry a nil for the session. No EggState = no bait egg = an
        -- unprimed cycle, which V3.1 also allows.
        if not EggState then EggState = data.eggState() SlotIdentity = SlotIdentity or data.slotIdentity() end
        if not EggState then
            stats.noEgg = stats.noEgg + 1
            return false, { reason = "no EggState on this executor" }
        end

        -- A Slot egg in the first area. Read straight from the field rather
        -- than through the cached list: this is one specific area and we want
        -- current truth, not a half-second-old ranking.
        local rec
        BX.try("bait.findEgg", function()
            for _, r in pairs(EggState.ReadFieldEggs().Records) do
                if r.AreaId == areaId and r.State == "Slot" and r.BoundsCFrame then
                    rec = r
                    break
                end
            end
        end)
        if not rec then
            stats.noEgg = stats.noEgg + 1
            log.trace("no egg available in %s", tostring(areaId))
            return false, { reason = "no bait egg" }
        end

        local pos = rec.BoundsCFrame.Position

        -- Same mover as every other leg. The pickup needs us inside the
        -- server's range or it answers "Get closer to the egg" - measured at
        -- 769 studs out, CarryFieldEgg with the correct slot key still refused
        -- for range alone.
        local movedAt = os.clock()
        move.travel{ to = pos, speed = K.APPROACH, arrive = K.ARRIVE,
                     carrying = false, cancel = cancel, tag = "bait approach" }
        if cancel and cancel() then
            stats.cancelled = stats.cancelled + 1
            return false, { reason = "cancelled" }
        end

        -- First-area eggs need a FirstAreaSlotKey or the server refuses outright.
        local slotKey = nil
        BX.try("bait.slotKey", function()
            if SlotIdentity and SlotIdentity.LooksLikeFirstAreaUid
               and SlotIdentity.LooksLikeFirstAreaUid(rec.Uid) then
                slotKey = SlotIdentity.SlotKey(rec.AreaId, rec.NestId)
            end
        end)

        -- PULLED BACK MID-GRAB: GO STRAIGHT BACK.
        --
        -- The first hop of a cycle sometimes gets a server Relocate ~0.2s after
        -- it lands, putting us back at spawn. V3.1's loop then spent the whole
        -- timeout asking to pick up an egg 90 studs away, gave up, and the
        -- retry a second later worked - that is "the first steal never picks
        -- up, the second one does".
        local got = false
        local deadline = os.clock() + dev.scale(K.PICKUP_WAIT)
        local rehops, tries = 0, 0
        local startPos = ch.root() and ch.root().Position

        while os.clock() < deadline and not got do
            if cancel and cancel() then
                stats.cancelled = stats.cancelled + 1
                return false, { reason = "cancelled" }
            end
            local here = ch.root()
            if not here then return false, { reason = "no character" } end

            -- Detected by distance rather than by an anticheat hook, so it
            -- works with no adapter armed.
            if startPos and (here.Position - pos).Magnitude > 60 and rehops < K.REHOPS then
                rehops = rehops + 1
                log.trace("server pulled us back - hopping again (%d/%d)", rehops, K.REHOPS)
                move.travel{ to = pos, speed = K.APPROACH, arrive = K.ARRIVE,
                             carrying = false, cancel = cancel, tag = "bait rehop" }
                deadline = os.clock() + dev.scale(K.PICKUP_WAIT)
            end

            tries = tries + 1
            local ok, res = pcall(function() return EggState.CarryFieldEgg(rec.Uid, slotKey) end)
            if ok and res == true then got = true break end
            RunService.Heartbeat:Wait()
        end

        if got then BX.profile.mark("bait_grab") end
        if not got then
            stats.noPickup = stats.noPickup + 1
            log.warn("could not pick up in %s after %d tries, %d rehops (%.2fs)",
                tostring(areaId), tries, rehops, os.clock() - t0)
            return false, { reason = "no pickup", tries = tries, rehops = rehops }
        end

        ---------- take the hit ----------

        BX.profile.mark("guard_contact")
        local guard = findGuard(areaId)
        local gpart = guardPart(guard)
        if gpart then
            -- It is asleep until the steal wakes it, and at WalkSpeed 16 it
            -- will not reach us in any useful time, so we go to it.
            local hh = ch.root()
            local char = ch.get()
            if hh and char then
                local gy = move.groundY(gpart.Position) or hh.Position.Y
                pcall(function()
                    char:PivotTo(CFrame.new(gpart.Position.X, gy, gpart.Position.Z))
                end)
            end
        else
            log.warn("no guard found in %s", tostring(areaId))
        end

        -- ANCHOR THROUGH THE HIT. NO FLING, NO GETTING UP.
        local hrp = ch.root()
        local anchorCF = hrp and hrp.CFrame
        if hrp then pcall(function() hrp.Anchored = true end) end

        local hitAt, witnessAt = nil, nil
        local dl = os.clock() + dev.scale(K.HIT_WAIT)

        while os.clock() < dl do
            if cancel and cancel() then break end
            local hh = ch.root()
            if not hh then break end

            hh.AssemblyLinearVelocity = Vector3.zero
            hh.AssemblyAngularVelocity = Vector3.zero
            if anchorCF then pcall(function() hh.CFrame = anchorCF end) end

            -- MORE THAN ONE WITNESS.
            --
            -- V3.1's first version read the hit only off the RigSync event. On
            -- executors where that never arrives (Delta reports), every prime
            -- read as "no hit", was retried, and the whole cycle was skipped -
            -- a new chicken egg every few seconds, a hundred times, never the
            -- target. These witnesses need nothing but the game's own state.
            local witnessed = false

            local hum = ch.humanoid()
            if hum and hum:GetState() == Enum.HumanoidStateType.Physics then
                witnessed = true   -- knocked down
            end
            if not witnessed then
                -- The egg's record went loose. NOT "Slot": ReadFieldEgg lags
                -- and still says Slot for a moment after a successful pickup,
                -- which would fake a hit and send the real steal out unprimed.
                local okR, r = pcall(EggState.ReadFieldEgg, rec.Uid)
                local st = okR and type(r) == "table" and r.State or nil
                witnessed = (st == "Dropped" or st == "GuardCarried")
            end

            -- A WITNESS IS AN EVENT. ONCE IT HAS FIRED, IT HAS FIRED.
            --
            -- The hold check used to sit INSIDE this `if witnessed then`, so it
            -- was only ever re-evaluated while the witness was STILL true. Every
            -- witness here is transient by nature - the Humanoid is in Physics
            -- for a moment, or the egg record reads Dropped/GuardCarried for a
            -- moment - and features/guard's own anti-ragdoll stands us back up
            -- within a frame or two, which is exactly what clears it. So the
            -- 0.35s hold was frequently never satisfied: witnessAt stayed set,
            -- hitAt stayed nil, the loop ran to the FULL HIT_WAIT and reported
            -- "no hit" having plainly seen one.
            --
            -- Measured on a live cycle before the fix, from the profile marks:
            --     guard_contact@0.25 ... unanchor@5.65     (no hit_detected)
            --     no hit in Forest after 5.65s (witness=yes)
            -- - the log line says witness=yes and no hit in the same breath, and
            -- HIT_WAIT is 4.0 scaled by the device, so that is 5.4s at mid tier
            -- and 8.8s at low tier of standing next to a guard that has already
            -- hit us.
            --
            -- The fix is not a shorter timeout. It is remembering the event: the
            -- stamp is taken the first time the witness is seen, and the hold is
            -- then counted on EVERY pass whether or not the witness is still
            -- true. Nothing else about the prime changes.
            if witnessed and not witnessAt then
                witnessAt = os.clock()
                log.info("witness seen @%.3f (+%.3fs into the prime)",
                    witnessAt, witnessAt - t0)
            end

            -- LET THE KNOCKDOWN LAND BEFORE LEAVING. These see the hit a
            -- few hundredths before the server's ragdoll arrives, and
            -- leaving on them sent the knockdown into the next teleport -
            -- measured: witness 7.50, TP 7.51, ragdoll 7.53, "landed 28
            -- studs from the egg", no pickup. That is what the hold is for,
            -- and it is why this is a hold rather than an immediate break.
            if witnessAt and (os.clock() - witnessAt) >= K.WITNESS_HOLD then
                BX.profile.mark("hit_detected")
                hitAt = os.clock()
                log.info("HIT CONFIRMED @%.3f (+%.3fs into the prime, hold=%.3fs)",
                    hitAt, hitAt - t0, hitAt - witnessAt)
                break
            end
            RunService.Heartbeat:Wait()
        end

        -- ALWAYS UNANCHOR, on every path out of the loop.
        do
            local hh = ch.root()
            if hh then pcall(function() hh.Anchored = false end) end
            BX.profile.mark("unanchor")
        end

        local took = hitAt ~= nil
        if took then stats.hits = stats.hits + 1 else stats.noHit = stats.noHit + 1 end

        log.info("%s in %s after %.2fs (tries=%d rehops=%d witness=%s tier=%s)",
            took and "HIT TAKEN" or "no hit", tostring(areaId), os.clock() - t0,
            tries, rehops, witnessAt and "yes" or "no", dev.tier)

        return took, {
            reason = took and "hit" or "no hit",
            areaId = areaId, tries = tries, rehops = rehops,
            elapsed = os.clock() - t0,
        }
    end

    return M
end)

--[[ ==== features/instant.lua ======================================== ]]
-- =============================================================================
-- FEATURES.INSTANT: teleport onto the egg and race the server for it
-- =============================================================================
--
--     local instant = BX.require("features.instant")
--     local ok, info = instant.take(uid, eggPos, { cancel = fn })
--
-- THIS IS V3.1's INSTANT STEAL. It is not "teleport, then check whether the
-- teleport was allowed" - that question is never asked, because the answer does
-- not matter.
--
-- HOW IT ACTUALLY WORKS. Measured on the live server by V3.1:
--
--     CarryFieldEgg is a BLOCKING RemoteFunction    ~160ms round trip
--     a bare PivotTo is relocated by the server     ~170ms later
--
-- So the server WILL pull us back - continuously, about six times a second.
-- That is fine. What matters is only where the character is at the instant the
-- server processes CarryFieldEgg. So:
--
--   1. A HOLD THREAD re-asserts PivotTo onto the egg every Heartbeat (~16ms),
--      which is ten times faster than the relocate can undo it.
--   2. THREE STAGGERED INVOKERS each call CarryFieldEgg in a loop, 0.05s apart,
--      so there is always a call in flight rather than a 160ms gap between them.
--   3. The first one to come back true wins and everything stops.
--
-- A PREVIOUS VERSION OF THIS PORT GOT IT WRONG, and the mistake is worth
-- recording: it teleported, waited K.TP_SETTLE (0.35s), measured the distance
-- to the egg and declared the teleport "refused" when it had been pulled back.
-- The relocate arrives at ~170ms, so waiting 350ms measured a guaranteed
-- failure every time - it reported "server refused" on a path V3.1 uses
-- successfully. The settle-and-verify step does not exist in V3.1 and is gone.
--
-- WHY THE HOLD THREAD MUST BE ABLE TO DIE WITHOUT US. V3.1 hit a case where the
-- flag that stops the hold never ran, so the hold thread kept re-asserting
-- position while the server relocated, several times a second, with nothing
-- left to stop it. The generation check and the scope below both exist for
-- that: the thread tests a generation number it captured, and the scope cancels
-- it outright on any exit path.

BX.module("features.instant", function(BX)
    local svc  = BX.require("core.services")
    local data = BX.require("core.data")
    local ch   = BX.require("core.character")
    local dev  = BX.require("core.device")
    local eggs  = BX.require("features.eggs")
    local guard = BX.require("features.guard")
    local log  = BX.require("boot.log").for_module("instant")

    local RunService = svc.RunService
    local M = {}

    local K = {
        TIMEOUT       = 3,     -- V3.1 tpStealTimeout. It was 8, and a refusal then
                               -- meant eight seconds stood on a nest for a guard
                               -- to reach; a hit lands inside ~0.4s.
        RACE_THREADS  = 3,     -- K.CARRY_RACE_THREADS
        RACE_STAGGER  = 0.05,  -- K.CARRY_RACE_STAGGER
        LIFT          = 2,     -- stand this far above the egg record
        -- Further than this from the egg when the race ends means the
        -- server had us somewhere else - a pull-back, not a near miss.
        PULLBACK_GAP  = 25,
        -- A REFUSAL THAT KEEPS COMING BACK IS AN ANSWER, NOT A RACE.
        --
        -- Measured on a failing recovery: 274 calls in 6.6s - about 41 remote
        -- invocations a second, sustained, all returning the same message, and
        -- the client dropped to tier=low under it. The race is meant to beat a
        -- ~170ms relocate, not to hammer a settled "no".
        --
        -- The first few calls stay flat out, because that is the window a real
        -- win happens in. After that, repeats of the SAME message slow down,
        -- and enough of them ends the attempt - the outer retry can re-read the
        -- record and try again from a fresh position.
        FREE_CALLS    = 12,    -- unthrottled attempts before we start easing off
        SAME_MSG_GAP  = 0.12,  -- wait between calls once a message repeats
        SAME_MSG_STOP = 30,    -- identical refusals before this attempt gives up
    }
    M.K = K

    -- THROUGH core.data. A private FindFirstChild + require here bypassed
    -- the one place that knows whether this executor can require game
    -- modules at all, and cached a miss forever. core.data probes once,
    -- lets a miss expire, and backs off - see core/data.lua.
    local EggState, SlotIdentity = data.eggState(), data.slotIdentity()

    -- Re-asked at use, not only at load: on a slow join the module can
    -- arrive after the hub did. core.data makes the re-ask cheap.
    local function ensureModules()
        if not EggState then EggState = data.eggState() end
        if not SlotIdentity then SlotIdentity = data.slotIdentity() end
        M.ready = (EggState ~= nil and type(EggState.CarryFieldEgg) == "function")
        return M.ready
    end
    ensureModules()
    if not M.ready then
        log.warn("EggState.CarryFieldEgg unavailable - instant steal disabled until it resolves")
    end

    local holdGen = 0
    local stats = { runs = 0, won = 0, lost = 0, cancelled = 0, calls = 0 }
    function M.stats() return table.clone(stats) end

    -- The slot key first-area eggs need, or nil. Without it the server refuses
    -- the carry outright for those.
    local function slotKeyFor(uid, areaId, nestId)
        local key = nil
        BX.try("instant.slotKey", function()
            if SlotIdentity and SlotIdentity.LooksLikeFirstAreaUid
               and SlotIdentity.LooksLikeFirstAreaUid(uid) then
                key = SlotIdentity.SlotKey(areaId, nestId)
            end
        end)
        return key
    end

    -- uid      the egg
    -- eggPos   Vector3 of its record
    -- opts     { cancel = fn, areaId = , nestId = , timeout = }
    function M.take(uid, eggPos, opts)
        opts = opts or {}
        local cancel = opts.cancel or function() return false end
        if not M.ready and not ensureModules() then return false, { reason = "no CarryFieldEgg" } end
        if typeof(eggPos) ~= "Vector3" then return false, { reason = "no egg position" } end

        local char = ch.get()
        if not char then return false, { reason = "no character" } end

        stats.runs = stats.runs + 1
        local t0 = os.clock()

        local target = CFrame.new(eggPos.X, eggPos.Y + K.LIFT, eggPos.Z)
        local slotKey = slotKeyFor(uid, opts.areaId, opts.nestId)
        -- Scaled: a weak client gets fewer frames, so it needs longer to land
        -- the same number of attempts.
        local deadline = os.clock() + dev.scale(opts.timeout or K.TIMEOUT)

        -- Everything below lives on this scope, so a cancel, a respawn or a
        -- re-execute takes the hold thread and every invoker with it.
        local sc = BX.scope("features.instant.race")
        holdGen = holdGen + 1
        local myGen = holdGen
        local won, tries, lastMsg = false, 0, nil
        local sameMsg, sameCount = nil, 0
        local bailed = false

        BX.profile.mark("target_tp")

        -- (1) THE HOLD. Re-assert position every frame, faster than the server
        --     can undo it.
        sc:spawn("hold", function()
            while not won and holdGen == myGen and os.clock() < deadline and sc:alive() do
                local c = ch.get()
                if c then pcall(function() c:PivotTo(target) end) end
                local h = ch.root()
                if h then
                    h.AssemblyLinearVelocity = Vector3.zero
                    h.AssemblyAngularVelocity = Vector3.zero
                end
                RunService.Heartbeat:Wait()
            end
        end)

        -- WAIT OUT THE SERVER KNOCKDOWN *HERE*, NOT BEFORE LEAVING.
        --
        -- V3.1 is explicit about this, at the point the wait would otherwise go
        -- (line 5395):
        --     "NO WAITING HERE. Prime, take the hit, LEAVE. I put a knockdown
        --      wait in this spot and it was wrong. The travel is what covers
        --      the knockdown."
        -- Its own knockdown wait lives inside carryEgg - i.e. AT THE EGG, after
        -- arriving - not between the hit and the departure.
        --
        -- An earlier version of this module had it before the teleport, which
        -- put 2.4 seconds of standing next to the Forest guard into every
        -- cycle: measured hit_detected@1.66 -> target_tp@4.04. The hold thread
        -- above is already running, so the wait now happens pinned to the
        -- target egg instead of parked beside the guard that just hit us.
        local heldFor = guard.waitForServerRelease(cancel)
        if cancel() then
            holdGen = holdGen + 1
            sc:destroy()
            stats.cancelled = stats.cancelled + 1
            return false, { reason = "cancelled", ms = (os.clock() - t0) * 1000 }
        end

        -- (2) THE INVOKERS, staggered so a call is always in flight.
        for i = 1, K.RACE_THREADS do
            sc:spawn("invoke" .. i, function()
                task.wait((i - 1) * K.RACE_STAGGER)
                while not won and not bailed and os.clock() < deadline and sc:alive() do
                    if cancel() then return end
                    tries = tries + 1
                    stats.calls = stats.calls + 1
                    local ok, res, msg = pcall(function()
                        return EggState.CarryFieldEgg(uid, slotKey)
                    end)
                    if msg ~= nil then lastMsg = tostring(msg) end

                    -- A REFUSAL WE ALREADY KNOW THE ANSWER TO IS NOT AN
                    -- ATTEMPT. If the server says we are still downed, more
                    -- calls cannot help - wait for the clock.
                    if type(msg) == "string" and msg:lower():find("downed") then
                        local left = guard.ragdollRemaining()
                        if left > 0 then task.wait(math.min(left, 0.25)) end
                    end

                    -- THROTTLE A REPEATING ANSWER.
                    if not won and type(msg) == "string" then
                        if msg == sameMsg then
                            sameCount = sameCount + 1
                        else
                            sameMsg, sameCount = msg, 1
                        end
                        if sameCount >= K.SAME_MSG_STOP then
                            bailed = true
                            return
                        end
                        if tries > K.FREE_CALLS and sameCount > 1 then
                            task.wait(K.SAME_MSG_GAP)
                        end
                    end
                    -- Re-checked AFTER the call returns: two can be in flight
                    -- at once and only the first winner may claim the carry.
                    if ok and res == true and not won then
                        won = true
                        return
                    end
                    if won then return end

                    -- EVERY PASS YIELDS. NO EXCEPTIONS.
                    --
                    -- This is the freeze, and it is the same class of bug V3.1
                    -- documents in its steal loop. Every task.wait above is
                    -- CONDITIONAL on msg being a string. When CarryFieldEgg
                    -- errors synchronously - pcall returns false and msg is nil
                    -- - none of them run, the remote never yields either, and
                    -- all three threads spin flat out until the deadline.
                    --
                    -- Three threads x 3s of a Luau loop that never yields locks
                    -- the client solid, and through regrab that is 4 attempts
                    -- per recovery and 2 recoveries per steal.
                    --
                    -- One Heartbeat costs ~16ms against a remote round trip
                    -- measured at 70-160ms, so the race is not meaningfully
                    -- slowed - and it can no longer become a freeze whatever
                    -- the remote does.
                    RunService.Heartbeat:Wait()
                end
            end)
        end

        -- (3) Wait for a winner.
        local cancelled = false
        while not won and not bailed and os.clock() < deadline do
            if cancel() then cancelled = true break end
            RunService.Heartbeat:Wait()
        end

        -- Stops the hold immediately; the scope then cancels every thread.
        holdGen = holdGen + 1
        sc:destroy()

        local ms = (os.clock() - t0) * 1000
        local gap = (function()
            local h = ch.root()
            return h and (h.Position - eggPos).Magnitude or -1
        end)()

        if cancelled then
            stats.cancelled = stats.cancelled + 1
            log.info("cancelled after %d calls in %.0fms", tries, ms)
            return false, { reason = "cancelled", calls = tries, ms = ms }
        end

        BX.profile.mark(won and "target_landed" or "target_lost")

        if won then
            stats.won = stats.won + 1
            eggs.markStolen(uid)
            log.info("WON uid=%s after %d calls in %.0fms (%d threads, gap %.1f, tier=%s)",
                tostring(uid), tries, ms, K.RACE_THREADS, gap, dev.tier)
            return true, { reason = "instant", calls = tries, ms = ms,
                           gap = gap, heldFor = heldFor }
        end

        stats.lost = stats.lost + 1

        -- WHY IT FAILED, NOT JUST THAT IT DID.
        --
        -- "Get closer to the egg" alone cannot be acted on. These four answer
        -- the question it raises - was our LOCAL position fine while the
        -- server had us somewhere else, or did the egg itself move or go?
        local rec = eggs.get(uid)
        local pulledBack = gap > K.PULLBACK_GAP
        local diag = ("localGap=%.1f eggState=%s eggMoved=%s pulledBack=%s%s"):format(
            gap,
            rec and tostring(rec.state) or "gone",
            rec and rec.pos and tostring((rec.pos - eggPos).Magnitude > 5) or "?",
            tostring(pulledBack),
            bailed and (" bailed after %d identical refusals"):format(sameCount) or "")

        log.warn("LOST uid=%s after %d calls in %.0fms (%s, last: %s, tier=%s)",
            tostring(uid), tries, ms, diag, tostring(lastMsg), dev.tier)

        return false, {
            reason = lastMsg or "no accept",
            calls = tries, ms = ms, gap = gap,
            pulledBack = pulledBack,
            eggState = rec and rec.state or "gone",
            eggGone = rec == nil,
            heldFor = heldFor,
        }
    end

    return M
end)

--[[ ==== features/autosteal.lua ====================================== ]]
-- =============================================================================
-- FEATURES.AUTOSTEAL: the run lifecycle
-- =============================================================================
--
--     local auto = BX.require("features.autosteal")
--     auto.setEnabled(true)     -- start, or do nothing if already running
--     auto.setEnabled(false)    -- stop and clean up completely
--     auto.isRunning()
--
-- THIS FILE IS THE LIFECYCLE, NOT THE STEAL.
--
-- The V3.1 steal method is being ported onto this spine stage by stage. What
-- is here is the part that has to be right BEFORE any of that lands, because
-- every one of V3.1's worst Auto Steal symptoms is a lifecycle failure rather
-- than a mistake in the steal itself:
--
--   "it gets laggy after a while"      two runs, or two movers, both live
--   "the game freezes when I turn it on"  a pass that never yielded
--   "it stops working after I die"     state captured from a dead character
--   "memory climbs"                    loops and connections from old runs
--
-- ONE RUN. EVER.
--
-- Two independent guarantees, because one is not enough:
--
--   1. A run token. Every run takes the next number and every loop checks that
--      it still holds the current one. A stale run returns on its next pass
--      even if something else forgot to stop it.
--   2. A scope. BX.scope("features.autosteal") retires any previous scope of
--      the same name before returning a new one, so starting a second run
--      cannot leave the first one's connections, threads or Instances behind.
--
-- Turning it off, dying, re-executing and unloading the menu all end in the
-- same place: stop(), which destroys the scope. There is no other cleanup path
-- to forget.
--
-- FAILURE DOES NOT MEAN RETRY IMMEDIATELY.
--
-- A failed egg used to send the loop straight round again. When the failure
-- was persistent - an egg that could not be picked up, a delivery the server
-- would not accept - that became a hot loop hammering remotes and burning
-- frames. Consecutive failures now back off, and a success resets it.

BX.module("features.autosteal", function(BX)
    local svc   = BX.require("core.services")
    local dev   = BX.require("core.device")
    local ch    = BX.require("core.character")
    local st    = BX.require("core.state")
    local eggs  = BX.require("features.eggs")
    local grab  = BX.require("features.grab")
    local move  = BX.require("features.movement")
    local carry = BX.require("features.carry")
    local bait  = BX.require("features.bait")
    local plot  = BX.require("features.plot")
    local adeath = BX.require("features.antideath")
    local guard  = BX.require("features.guard")
    local rs     = BX.require("core.restore")
    local instant = BX.require("features.instant")
    local regrab = BX.require("features.regrab")
    local hswap  = BX.require("features.humanoid")
    local log   = BX.require("boot.log").for_module("autosteal")

    local M = {}

    -- Backoff after consecutive failures: 1s, 2s, 4s, 8s, capped. Scaled for
    -- the device, because a weak machine needs more room between attempts, not
    -- less. A success puts it straight back to zero.
    local BACKOFF_BASE = 1.0
    local BACKOFF_CAP  = 8.0

    -- Nothing to steal is NOT a failure. V3.1 switched Auto Steal off after
    -- eight idle cycles, which could not tell a broken loop apart from the
    -- ordinary case of everything matching your filter having just been taken.
    -- Idle waits, and keeps waiting.
    local IDLE_WAIT = 0.5

    -- THE STAGES A CYCLE MOVES THROUGH.
    --
    -- Named so a failure can invalidate only what actually needs repeating. A
    -- failed target grab used to send the whole run back to the Forest guard
    -- for a fresh bait, and V3.1 added an at-target retry specifically to stop
    -- that: its own trace shows a cycle thrown away for a pull-back, and the
    -- NEXT cycle - a fresh prime and a second guard hit later - taking the very
    -- same egg.
    M.STATE = {
        -- PREPARATION IS NOT COMPLETION.
        --
        -- A run that starts with an egg already in our hands has to get rid of
        -- it before it can steal anything - the server refuses every later
        -- pickup with "Already carrying an egg". That trip goes to the safe
        -- zone and it looks exactly like a delivery, which is precisely why it
        -- was being counted as one: enabling Auto Steal at the plot flew to the
        -- safe zone, dropped the egg we happened to be holding, reported
        -- DELIVERED and switched itself off before the SELECTED egg had been
        -- touched. The user then had to toggle it again to get a real steal.
        --
        -- So preparation has its own name and its own terminal flag.
        PREP_DELIVER_HELD = "PREP_DELIVER_HELD",
        READY_TO_STEAL    = "READY_TO_STEAL",
        BAIT_NOT_DONE     = "BAIT_NOT_DONE",
        BAIT_DONE         = "BAIT_DONE",
        AT_TARGET         = "AT_TARGET",
        TARGET_GRAB_RETRY = "TARGET_GRAB_RETRY",
        CARRYING          = "CARRYING",
        RETURNING         = "RETURNING",
        DELIVERED         = "DELIVERED",
    }

    -- THE RUN'S OWN TRAIL, not the cycle's.
    --
    -- One line per phase change for the whole run, so "it stopped at the safe
    -- zone" can be read back as a sequence rather than guessed at:
    --
    --     run 1: START -> PREP_DELIVER_HELD -> READY_TO_STEAL -> BAIT_DONE
    --            -> AT_TARGET -> CARRYING -> RETURNING -> DELIVERED -> STOP
    --
    -- Kept on the module so a test can assert the order.
    local phases = {}
    local phaseRun = 0

    local function phase(token, name, detail)
        if token ~= phaseRun then
            phases, phaseRun = {}, token
        end
        phases[#phases + 1] = name
        log.info("run %d: phase %s%s", token, name,
            detail and (" (" .. tostring(detail) .. ")") or "")
    end

    function M.phases() return table.clone(phases) end

    local TARGET_RETRIES = 2

    -- How long to wait for a character after the bait hit kills us.
    local RESPAWN_WAIT = 12

    -- Anyone who needs to know the run ended - the UI toggle, mainly. Fired on
    -- a fresh thread because a listener will want to write to an Instance, and
    -- the thread that ran the cycle has called into game code and is narrowed.
    local stopListeners = {}

    function M.onStop(fn)
        stopListeners[#stopListeners + 1] = fn
    end

    -- CONFIRMED DELIVERIES ONLY, and it cannot affect the run.
    --
    -- Fired where a cycle reaches DELIVERED - after the server has claimed the
    -- egg - with the egg record the cycle already holds, so a listener needs no
    -- second field read. Each listener runs on its OWN thread inside BX.try: a
    -- webhook that hangs, throws or gets rate-limited cannot delay or stop the
    -- steal loop, which is the whole point of not calling them inline.
    local deliveredListeners = {}

    function M.onDelivered(fn)
        deliveredListeners[#deliveredListeners + 1] = fn
    end

    local function fireDelivered(target)
        if not target then return end
        for _, fn in ipairs(deliveredListeners) do
            task.spawn(function() BX.try("autosteal.onDelivered", fn, target) end)
        end
    end

    local runToken = 0
    local running  = false
    local cycles   = 0
    local sc       = nil
    local failures = 0

    -- ONE ENGINE, TWO CALLERS, AND THE FILTER BELONGS TO THE CALLER.
    --
    -- Main starts a run for ONE egg the user named and stops on delivery. Farm
    -- starts a run for a DESCRIPTION - areas, rarities, ordering - and keeps
    -- going. Same bait, same grab, same carry, same cleanup; only the options
    -- differ, and the options belong to whichever tab started the run.
    --
    -- V3.1 learned this the hard way and says so at BX.farmWanted (line 1670):
    -- "It used to be global, and that is the bug: pick Abyss Ocean on Farm, turn
    -- Farm's Auto Steal off, start Main's instead, and Main quietly kept stealing
    -- from Abyss Ocean. Nothing on Main said a filter existed, so there was no
    -- way to work out why it was ignoring the rest of the map."
    --
    -- So options are stored PER SOURCE and the live run reads only the source
    -- that started it. Changing Farm's dropdowns while Main is running cannot
    -- reach Main's run, and vice versa.
    local opts     = {}       -- the live run's options
    local optsFor  = {}       -- source -> that tab's options
    local owner    = nil      -- which source started the live run

    ---------- the systems this orchestrates ----------
    --
    --   features.bait      the Forest prime and the guard hit
    --   features.eggs      what exists and what it is worth
    --   features.movement  every leg, spoofed or not
    --   features.grab      the prompt fire and the confirmation
    --   features.carry     up, across, down, and the claim
    --   features.plot      where home is, and who says the egg is ours
    --
    -- This file owns the ORDER and the run's lifetime. It holds no movement,
    -- egg or carry state of its own, which is the whole reason it can stop
    -- cleanly: there is nothing here to leave behind.
    --
    -- NOT YET PORTED, and therefore not yet part of a cycle: the anticheat
    -- hold (movement runs unspoofed, so the outbound bracket stays at the
    -- measured-safe 500) and anti-hit on the real carry leg.

    ---------- resource snapshot ----------

    -- Taken before and after every cycle. Anything here that climbs with each
    -- steal is a leak the cycle owns, and unlike total process memory these are
    -- numbers we control.
    local function snapshot()
        local h = BX.profile.health()
        local e = eggs.stats()
        return {
            scopes = h.scopes, conns = h.conns, insts = h.insts, threads = h.threads,
            eggList = e.listSize, eggValues = e.valueCache,
        }
    end

    local SNAP_KEYS = { "scopes", "conns", "insts", "threads", "eggList", "eggValues" }

    local function diff(a, b)
        local out = {}
        for _, k in ipairs(SNAP_KEYS) do
            local d = (b[k] or 0) - (a[k] or 0)
            if d ~= 0 then out[#out + 1] = ("%s %+d"):format(k, d) end
        end
        return #out > 0 and table.concat(out, " ") or "no change"
    end

    -- NO RECOVERY STAGE HERE, DELIBERATELY.
    --
    -- An earlier version of this port added one. V3.1 does not have it, and
    -- says why at the point it would go (line 5395):
    --     "NO WAITING HERE. Prime, take the hit, LEAVE. I put a knockdown wait
    --      in this spot and it was wrong. The travel is what covers the
    --      knockdown, and on a normal trip it covers all of it."
    --
    -- The stage was invented to work around deaths that were really caused by
    -- two other things: the outbound running at 500 instead of 1200, and
    -- features.guard not being armed at all. Both are fixed, so the workaround
    -- goes rather than being kept "just in case" - it would only mask the next
    -- real failure.

    ---------- the cycle ----------

    -- V3.1 cycle, stage by stage:
    --     bait -> take the hit -> target -> grab -> up -> home -> down -> claimed
    --
    -- Every stage is timed separately, because "Auto Steal is laggy" is not
    -- actionable and "cross took 9.4s on a tier=low client" is.
    local function runCycle(token, cancel)
        local cycle = { t0 = os.clock(), stages = {} }
        BX.profile.mark("cycle_start")

        local function stage(name, fn)
            if cancel() then return false, { reason = "cancelled" } end
            local s0 = os.clock()
            local ok, info = fn()
            cycle.stages[#cycle.stages + 1] = {
                name = name, ms = (os.clock() - s0) * 1000, ok = ok and true or false,
            }
            return ok, info
        end

        -- ALREADY HOLDING ONE? DELIVER THAT FIRST.
        --
        -- A run that ended while carrying - toggled off mid-carry, a failed
        -- delivery, an error - leaves the egg in our hands. Every later attempt
        -- is then refused by the server with "Already carrying an egg", and the
        -- bait fails too, so the whole run spins without ever picking anything
        -- up. That is the "it does not pick the egg at the 2nd try" report,
        -- measured here as three consecutive cycles failing that way.
        --
        -- V3.1 checks the same thing at the top of its loop and says so:
        -- "still carrying ... - going home before anything else". The authority
        -- is the game, not our own flag.
        local held = eggs.carryingUid()
        if held then
            -- IS THIS THE JOB, OR IS IT PREPARATION?
            --
            -- Holding the egg the user actually asked for means the job is one
            -- delivery away and finishing it IS the run. Holding anything else
            -- - whatever was in our hands when Auto Steal was switched on -
            -- means this trip only clears our hands so a steal can start.
            --
            -- Both fly to the safe zone and look identical on screen. Only the
            -- first one is allowed to end the run.
            local isObjective = (opts.uid ~= nil) and (held == opts.uid)
            cycle.prep = not isObjective
            cycle.state = isObjective and M.STATE.RETURNING
                or M.STATE.PREP_DELIVER_HELD
            phase(token, cycle.state, "holding " .. tostring(held))
            cycle.recovered = held
            log.info("already carrying %s - %s", held,
                isObjective and "this is the selected egg, delivering to finish"
                or "not the selected egg, clearing our hands first")
            local ok2, info2 = stage("carry held", function()
                return carry.home(held, { cancel = cancel })
            end)
            if ok2 then
                cycle.target = { name = isObjective and "selected egg" or "held egg",
                                 uid = held }
                if isObjective then
                    cycle.state = M.STATE.DELIVERED
                    cycle.terminal = true
                    phase(token, cycle.state, held)
                    return true, "delivered", cycle
                end
                -- Hands clear. The SAME run carries straight on into the steal.
                cycle.state = M.STATE.READY_TO_STEAL
                cycle.terminal = false
                phase(token, cycle.state, "hands clear after prep")
                return true, "prep: held egg delivered", cycle
            end
            -- Could not deliver it either. Say so and let the backoff apply,
            -- rather than starting a bait that cannot work while it is held.
            return false, "held egg: " .. tostring(info2 and info2.reason), cycle
        end

        -- Hands clear from the start: this cycle is the steal itself.
        if cycle.state == nil then
            cycle.state = M.STATE.READY_TO_STEAL
            phase(token, cycle.state)
        end

        -- 0. NOTHING TO GO FOR = NO BAIT. Farm only (opts.pick).
        --
        -- The bait ran BEFORE the target was chosen (V3.1's order, kept below
        -- for the real steal), so a filter that matched nothing still primed
        -- the bait every cycle: the character walked the Forest, picked up,
        -- dropped, came back, learned there was no target, backed off, and did
        -- it again. On a phone that reads as "Auto Steal jitters at the base
        -- with Drop flashing and never goes anywhere" - the Farm report. The
        -- pick is a walk of the cached list, so asking first costs nothing,
        -- and the answer is an idle wait (not a failure, not a backoff).
        local preTarget = nil
        if opts.pick and not opts.uid then
            local okPre, pre, whyPre = pcall(opts.pick)
            if not okPre then
                return false, "target picker failed: " .. tostring(pre), cycle
            end
            if not pre then
                return false, "nothing to steal"
                    .. (whyPre and (" (" .. tostring(whyPre) .. ")") or ""), cycle
            end
            preTarget = pre
        end

        -- 0b. A TARGET IN THE BAIT AREA IS NOT BAITED FOR. V3.1, verbatim:
        --
        --     if BX.primeEnabled and not heldEggUid
        --        and target.AreaId ~= BX.firstAreaId() then <prime>
        --
        -- The prime takes a Forest egg and walks into the Forest guard on
        -- purpose, which leaves that guard chasing. Priming and then
        -- teleporting to a FOREST target lands you next to the guard you just
        -- woke: caught, no pickup, "teleports to the egg but doesn't pick it
        -- up". Main rarely picks a Forest egg by name; Farm's picker does
        -- whenever the best income is there - which is the whole regression.
        -- Same rule for both owners, so Main with a Forest target is fixed too.
        local firstArea = bait.firstAreaId(0)
        local inBaitArea = nil
        if opts.uid then
            local want = eggs.get(opts.uid)
            inBaitArea = want and firstArea and want.areaId == firstArea or false
        elseif preTarget then
            inBaitArea = firstArea ~= nil and preTarget.areaId == firstArea
        end

        -- 1. THE BAIT. A failed prime is NOT a failed cycle: a missing bait egg
        --    is an ordinary state of the field, and V3.1 carries on without it
        --    rather than refusing to steal.
        local primed = false
        if inBaitArea then
            log.info("target is in the bait area (%s) - not priming, going straight for it (V3.1 rule)",
                tostring(firstArea))
            cycle.baitSkipped = true
        else
            primed = stage("bait", function()
                return bait.prime({ cancel = cancel })
            end)
        end
        cycle.primed = primed and true or false
        if cancel() then return false, "cancelled", cycle end

        -- 2. THE TARGET, chosen AFTER the bait. The bait takes seconds and the
        --    best egg may well have been taken in that time.
        -- A CHOSEN EGG IS A CHOSEN EGG.
        --
        -- When the user has picked one, we target THAT and wait for it rather
        -- than quietly stealing something else - a run that silently retargets
        -- is indistinguishable from one that ignored the dropdown. It is only
        -- abandoned when the egg is genuinely gone.
        local target
        if opts.uid then
            local want = eggs.get(opts.uid)
            if not want then
                return false, "selected egg is gone", cycle
            end
            local takeable = (want.state == "Slot" or want.state == "Dropped")
            if not takeable or not want.pos then
                -- Mid-cycle somewhere (carried, guard-held). Wait for it.
                return false, "waiting for the selected egg (" .. tostring(want.state) .. ")", cycle
            end
            target = want
        elseif opts.pick then
            -- A DESCRIPTION, NOT A NAME. Farm hands in a closure and this file
            -- stays ignorant of what an area or a rarity is: it asks for a
            -- target and gets one, chosen fresh every cycle from the egg list
            -- that is already cached. Re-picking per cycle is the whole point -
            -- holding one selection is what left V3.1 sitting on a chicken nest
            -- while a secret was up somewhere else.
            --
            -- Unless the bait was skipped FOR this target: no time has passed,
            -- and re-picking could swap a Forest target for a non-Forest one we
            -- then steal unprimed. The pre-pick is the pick.
            local ok2, want, why2 = true, preTarget, nil
            if not (cycle.baitSkipped and preTarget) then
                ok2, want, why2 = pcall(opts.pick)
            end
            if not ok2 then
                return false, "target picker failed: " .. tostring(want), cycle
            end
            target = want
            -- The picker's own reckoning, so "it picked nothing" is answerable
            -- without guessing which stage of the filter emptied the list.
            if not target then
                return false, "nothing matches the filter"
                    .. (why2 and (" (" .. tostring(why2) .. ")") or ""), cycle
            end
        else
            target = eggs.best()
        end
        if not target then return false, "nothing to steal", cycle end
        cycle.target = target

        local here = ch.root()
        cycle.distance = here and (target.pos - here.Position).Magnitude or -1

        -- 3+4. INSTANT STEAL: teleport onto the egg and race the server.
        --
        -- This is one stage, not two, because V3.1 does not separate them. It
        -- does NOT verify that the teleport was accepted - the server pulls us
        -- back about six times a second and that is expected. A hold thread
        -- re-asserts position every frame while three staggered threads hammer
        -- CarryFieldEgg; whoever is first to come back true wins.
        --
        -- See features/instant.lua for the measured timings.
        cycle.state = M.STATE.BAIT_DONE
        phase(token, cycle.state, cycle.primed and "primed"
            or (cycle.baitSkipped and "bait skipped: target in the bait area" or "no bait egg"))
        -- The trace the report asked for, in one line per cycle.
        log.info("target uid=%s name=%s area=%s rarity=%s state=%s dist=%.0f primed=%s",
            tostring(target.uid), tostring(target.name), tostring(target.areaId),
            tostring(target.rarity), tostring(target.state), cycle.distance or -1,
            tostring(cycle.primed))

        local took, inInfo
        local retries = 0
        for attempt = 0, TARGET_RETRIES do
            cycle.state = (attempt == 0) and M.STATE.AT_TARGET or M.STATE.TARGET_GRAB_RETRY
            phase(token, cycle.state, target.name)
            took, inInfo = stage(attempt == 0 and "instant" or ("regrab" .. attempt), function()
                return instant.take(target.uid, target.pos, {
                    cancel = cancel,
                    areaId = target.areaId, nestId = target.nestId,
                })
            end)
            if took or cancel() then break end

            -- RETRY AT THE TARGET, NOT FROM THE FOREST.
            --
            -- Only worth repeating when the reason is positional - the server
            -- pulled us back, so the grab asked from the wrong place. An egg
            -- that is gone, or already carried by someone else, will not become
            -- grabbable by asking again.
            local st = inInfo and inInfo.eggState
            local retryable = inInfo and (inInfo.pulledBack
                or st == "Slot" or st == "Dropped")
            if not retryable or attempt == TARGET_RETRIES then break end

            -- Re-read the record: it may have moved while we were refused.
            local fresh = eggs.get(target.uid)
            if not fresh or not fresh.pos then break end
            target.pos = fresh.pos
            retries = retries + 1
            log.info("target retry %d/%d (reason=%s state=%s pulledBack=%s)",
                attempt + 1, TARGET_RETRIES,
                tostring(inInfo and inInfo.reason), tostring(st),
                tostring(inInfo and inInfo.pulledBack))
        end
        cycle.grabRetries = retries

        if cancel() then return false, "cancelled", cycle end

        if took then
            cycle.state = M.STATE.CARRYING
            phase(token, cycle.state, "instant")
            cycle.transition = "tp"
            cycle.calls = inInfo and inInfo.calls
            cycle.tpGap = inInfo and inInfo.gap
        else
            -- THE FALLBACK, and only now. The race ran for its full timeout
            -- and the server never accepted, so travel there properly and use
            -- the prompt path instead. At 1200 studs/s with the WalkSpeed
            -- claim, exactly as V3.1 flies it.
            cycle.transition = "arc_fallback"
            cycle.instantFail = inInfo and inInfo.reason
            cycle.instantDiag = inInfo

            local reached, moveInfo = stage("approach", function()
                return move.travel{
                    to = target.pos, speed = move.outboundSpeed(),
                    arrive = 4, carrying = false, cancel = cancel, tag = "approach",
                }
            end)
            if cancel() then return false, "cancelled", cycle end
            if not reached then
                return false, "approach: " .. tostring(moveInfo and moveInfo.reason), cycle
            end

            local grabbed, grabInfo = stage("grab", function()
                return grab.take(target.uid, { pos = target.pos, cancel = cancel })
            end)
            if cancel() then return false, "cancelled", cycle end
            if not grabbed then
                -- Give it a rest rather than handing the same egg straight back
                -- on the next pass and repeating the whole bait for it.
                eggs.markUnreachable(target.uid)
                return false, "grab: " .. tostring(grabInfo and grabInfo.reason), cycle
            end
            cycle.state = M.STATE.CARRYING
            phase(token, cycle.state, "prompt")
        end

        -- 5. CARRY HOME: up, across, down, and wait to be claimed.
        cycle.state = M.STATE.RETURNING
        phase(token, cycle.state, target.name)
        local delivered, carryInfo = stage("carry", function()
            return carry.home(target.uid, { cancel = cancel })
        end)

        -- DROPPED IN TRANSIT: GO BACK FOR THE EGG, NOT FOR THE GUARD.
        --
        -- V3.1 BX.regrabEgg chases the dropped egg and re-grabs it. It does
        -- NOT restart the bait cycle - that would be a full cross-map trip, a
        -- second knockdown and a fresh prime, only to end up chasing the same
        -- egg anyway. Bounded at MAX_PER_STEAL recoveries so a repeatedly
        -- dropped egg cannot become a fast restart loop, and it reuses THIS
        -- run rather than starting another.
        local recoveries = 0
        while not delivered and not cancel()
              and carryInfo and carryInfo.reason
              and tostring(carryInfo.reason):find("dropped in transit", 1, true)
              and recoveries < regrab.K.MAX_PER_STEAL do

            -- Bounded at MAX_PER_STEAL and every call in the body yields, so
            -- this cannot spin - but it costs one frame at most twice a steal
            -- to make that true by construction rather than by argument.
            svc.RunService.Heartbeat:Wait()

            recoveries = recoveries + 1
            cycle.recoveries = recoveries
            cycle.state = M.STATE.TARGET_GRAB_RETRY

            local back, rinfo = stage("recover" .. recoveries, function()
                return regrab.recover(target.uid, {
                    cancel = cancel,
                    areaId = target.areaId, nestId = target.nestId,
                })
            end)
            cycle.dropRecovery = rinfo and rinfo.recovery or "?"

            if not back then
                return false, "drop recovery: " .. tostring(rinfo and rinfo.reason), cycle
            end

            -- Got it back. Carry it home again.
            cycle.state = M.STATE.RETURNING
            delivered, carryInfo = stage("carry" .. recoveries, function()
                return carry.home(target.uid, { cancel = cancel })
            end)
        end

        if cancel() then return false, "cancelled", cycle end
        if not delivered then
            return false, "carry: " .. tostring(carryInfo and carryInfo.reason), cycle
        end

        -- THE ONE TERMINAL SUCCESS: the egg this run set out to take is home.
        cycle.state = M.STATE.DELIVERED
        cycle.terminal = true
        phase(token, cycle.state, target.name)
        fireDelivered(target)
        return true, "delivered", cycle
    end

    local function reportCycle(ok, why, cycle, before, after)
        local parts = {}
        for _, s in ipairs(cycle.stages) do
            parts[#parts + 1] = ("%s=%.0fms%s"):format(s.name, s.ms, s.ok and "" or "!")
        end
        -- On a failure, print where the character actually was at each mark.
        -- "it died somewhere in the cycle" is not actionable; a health of 0 at
        -- unanchor and 100 at target_tp is.
        if not ok then
            local marks = BX.profile.marksSince(cycle.t0)
            if #marks > 0 then
                log.warn("timeline: %s", table.concat(marks, " | "))
            end
        end

        local level = ok and log.info or log.warn
        level("cycle %s in %.2fs [%s] target=%s dist=%.0f %s | %s",
            ok and "DELIVERED" or ("FAILED " .. tostring(why)),
            os.clock() - cycle.t0, table.concat(parts, " "),
            cycle.target and cycle.target.name or "-",
            cycle.distance or -1,
            ("state=%s transition=%s calls=%s retries=%d recoveries=%d%s primed=%s%s"):format(
                cycle.state or "?", cycle.transition or "?",
                tostring(cycle.calls or "-"), cycle.grabRetries or 0,
                cycle.recoveries or 0,
                cycle.dropRecovery and (" drop_recovery=" .. cycle.dropRecovery) or "",
                tostring(cycle.primed),
                cycle.instantFail and (" instantFail=" .. tostring(cycle.instantFail)
                    .. " pulledBack=" .. tostring(cycle.instantDiag and cycle.instantDiag.pulledBack)
                    .. " eggState=" .. tostring(cycle.instantDiag and cycle.instantDiag.eggState)) or ""),
            diff(before, after))
    end

    ---------- the loop ----------

    -- A run that only ever "prepares" is not progressing. Three hand-clearing
    -- trips is already generous: it takes one.
    local MAX_PREPS = 3
    local lastIdleWhy = nil

    local function runLoop(token)
        log.info("run %d: begin (tier=%s)", token, dev.tier)
        phase(token, "START", "tier=" .. tostring(dev.tier))
        local preps = 0

        while running and token == runToken and BX.alive() do
            -- EVERY PASS YIELDS AT LEAST ONE FRAME. NO EXCEPTIONS.
            --
            -- This is not a nicety. V3.1 caught a live case where a carried
            -- egg would not deliver, the return found itself already home and
            -- returned instantly, and the loop went round again - hundreds of
            -- passes inside 0.03s, never yielding. A Luau loop that never
            -- yields freezes the whole client, and it hits phones and
            -- emulators hardest. One Heartbeat per pass costs nothing next to
            -- a steal and makes a spin of that kind impossible, whatever the
            -- cause turns out to be.
            svc.RunService.Heartbeat:Wait()
            if not running or token ~= runToken then break end

            local isCancelled = function()
                return (not running) or token ~= runToken or (not BX.alive())
            end

            local before = snapshot()
            local ok, why, cycle = runCycle(token, isCancelled)
            local after = snapshot()

            -- AN IDLE PASS IS NOT A FAILED CYCLE. "nothing to steal" comes back
            -- twice a second while a Farm filter matches nothing; reporting
            -- each one as "cycle FAILED" filled the 400-line trace with the
            -- same sentence and buried the one line that explains it. Logged
            -- once per distinct reason instead.
            local idle = type(why) == "string"
                and (why:find("^nothing to steal") or why:find("^nothing matches the filter")) or false
            if idle then
                if why ~= lastIdleWhy then
                    lastIdleWhy = why
                    log.info("idle: %s", why)
                end
            else
                lastIdleWhy = nil
                if cycle then reportCycle(ok, why, cycle, before, after) end
            end

            if why == "cancelled" then break end

            if ok and not (cycle and cycle.terminal) then
                -- A SUCCESSFUL PASS THAT WAS NOT THE JOB.
                --
                -- Right now this is only ever the held-egg hand-clearing trip.
                -- It succeeded, so the backoff resets - but it is preparation,
                -- the run stays alive, the toggle stays on, and the next pass
                -- is the steal the user actually asked for.
                --
                -- Bounded: if we keep "preparing" we are not making progress,
                -- and something is handing us an egg we cannot get rid of.
                failures = 0
                preps = preps + 1
                if preps > MAX_PREPS then
                    log.warn("%d preparation passes without a steal - stopping",
                        preps)
                    return "ended"
                end
                log.info("preparation complete (%s) - continuing the same run",
                    tostring(why))
            elseif ok and opts.continuous then
                -- FARM: A DELIVERY IS THE END OF A CYCLE, NOT OF THE JOB.
                --
                -- The same terminal delivery that stops Main keeps Farm going.
                -- The next pass re-picks from the filter, so the run follows the
                -- field instead of holding a stale choice, and it only ends when
                -- the user turns it off. No second loop, no second worker - this
                -- is the one run, going round again.
                failures = 0
                cycles = cycles + 1
                log.info("delivered (%d this run) - continuing", cycles)
            elseif ok then
                failures = 0
                cycles = cycles + 1
                -- THE RUN IS FINISHED.
                --
                -- A confirmed delivery is the end of the job, not the start of
                -- the next one. Picking a fresh target and going back to the
                -- Forest guard on its own is not what "steal this egg" means,
                -- and it is how a run that was doing exactly what was asked
                -- ends up looking like it ignored the dropdown.
                --
                -- ONLY the selected egg reaching the safe zone gets here. The
                -- preparation trip above goes to the same place and is not a
                -- completion; conflating the two is what switched Auto Steal
                -- off before it had stolen anything.
                log.info("delivered - run complete")
                return "delivered"
            elseif (type(why) == "string" and why:find("^nothing to steal"))
                or why == "nothing matches the filter"
                or (type(why) == "string" and why:find("^nothing matches the filter"))
                or why == "selected egg is gone"
                or (type(why) == "string" and why:find("waiting for the selected egg", 1, true)) then
                -- NOT a failure. Everything matching the filter has just been
                -- taken and has not respawned; waiting is correct and it must
                -- not feed the backoff.
                task.wait(dev.scale(IDLE_WAIT))
            else
                failures = failures + 1
                local wait = math.min(BACKOFF_BASE * (2 ^ (failures - 1)), BACKOFF_CAP)
                wait = dev.scale(wait)
                log.warn("backing off %.1fs (failure %d)", wait, failures)
                task.wait(wait)
            end
        end

        log.info("run %d: ended", token)
        return "ended"
    end

    ---------- start / stop ----------

    local function stop(reason)
        if not running then return end
        running = false
        -- Bumping the token retires any loop that is mid-pass, even one that
        -- is blocked in a wait and cannot see `running` yet.
        runToken = runToken + 1
        st.autoStealOn = false

        if sc then
            -- Everything the run created: connections, threads, movers,
            -- Instances, tweens. One call, no list to keep in sync.
            sc:destroy()
            sc = nil
        end
        failures = 0
        -- Captured before clearing: a stop listener needs to know WHICH tab's
        -- run ended, or every tab resets its toggle on every stop.
        local whose = owner
        owner = nil
        opts = {}
        -- A run cancelled mid-leg can leave PlatformStand on, an inflated
        -- WalkSpeed, noclip active or the character anchored. None of those
        -- belong to a scope, so they are undone explicitly.
        BX.try("autosteal.antideath", adeath.disarm)
        BX.try("autosteal.humanoid", hswap.disarm)
        BX.try("autosteal.guard", guard.disarm)
        BX.try("autosteal.resetMovement", move.reset)
        BX.try("autosteal.unanchor", function()
            local hrp = ch.root()
            if hrp and hrp.Anchored then hrp.Anchored = false end
        end)

        -- ONE CLEANUP PATH, AND IT IS IDEMPOTENT.
        --
        -- Every terminal path - delivered, toggled off, failed grab, dropped
        -- egg, recovery failure, death, target gone, re-execute, an error -
        -- arrives here. The features above disarm themselves; core.restore then
        -- puts back every character value that was captured before it was
        -- changed, using the value it actually had rather than a guess.
        --
        -- restoreAll is safe to call twice. audit() reports anything still
        -- differing - a clean run prints PASS, and anything else names exactly
        -- what was left behind. That is the difference between cleanup that
        -- works and cleanup that is believed to work.
        local restored, skipped, failed = 0, 0, 0
        BX.try("autosteal.restore", function()
            restored, skipped, failed = rs.restoreAll()
        end)

        local leftovers = {}
        BX.try("autosteal.audit", function() leftovers = rs.audit() end)

        if #leftovers == 0 and failed == 0 then
            log.info("autosteal cleanup: PASS (%d restored, %d skipped)",
                restored, skipped)
        else
            log.warn("autosteal cleanup: %d restored, %d skipped, %d FAILED%s",
                restored, skipped, failed,
                #leftovers > 0
                    and (" | still modified: " .. table.concat(leftovers, "; ")) or "")
        end

        log.info("stopped (%s) after %d cycles", reason or "requested", cycles)
        phase(phaseRun, "STOP", reason or "requested")
        log.info("run %d trail: %s", phaseRun, table.concat(phases, " -> "))

        -- On its own thread: a listener writes to a UI Instance, and this
        -- thread has been through game code and cannot touch one.
        local why = reason or "requested"
        for _, fn in ipairs(stopListeners) do
            task.spawn(function() BX.try("autosteal.onStop", fn, why, whose) end)
        end
    end

    -- WHAT THIS EXECUTOR CAN STEAL WITH, SAID ONCE, BEFORE THE RUN.
    --
    -- The pickup has two paths: the instant race (EggState.CarryFieldEgg,
    -- which needs require() on the game's modules) and the prompt grab
    -- (fireproximityprompt, or the InputHoldBegin fallback). An executor with
    -- neither has no steal, and the run used to discover that one cycle at a
    -- time as "no CarryFieldEgg" then "no prompt", then back off, forever -
    -- a switch that stayed on and did nothing. Refused up front instead, with
    -- the reason in the log and returned to the tab.
    function M.capability()
        local exec = BX.require("core.exec")
        local paths = {}
        if instant.ready then paths[#paths + 1] = "instant (CarryFieldEgg)" end
        if exec.can.prompts then paths[#paths + 1] = "prompt (" .. tostring(exec.promptVia) .. ")" end
        if #paths == 0 then
            return false, "Auto Steal cannot run on this executor: no game-module require ("
                .. tostring(exec.gameRequireWhy) .. ") and no proximity prompt path"
        end
        return true, table.concat(paths, " + ")
    end

    local function start(src)
        if running then return end
        local okCap, capWhy = M.capability()
        if not okCap then
            log.error("%s", capWhy)
            return false, capWhy
        end
        owner = tostring(src or "main")
        opts = optsFor[owner] or {}
        log.info("run starting for %s - pickup via %s", owner, capWhy)
        -- Stop first even though `running` is false: a previous scope can
        -- outlive its run if something went wrong, and this is the one place
        -- that guarantees it does not.
        if sc then sc:destroy() end

        runToken = runToken + 1
        running  = true
        st.autoStealOn = true
        sc = BX.scope("features.autosteal")

        local token = runToken

        -- A respawn does not restart the run; it resets what the run knows
        -- about the character. Rebuilding here is what produced duplicate
        -- workers in V3.1 - five deaths, five loops.
        ch.onSpawn(sc, "autosteal.respawn", function()
            if not running or token ~= runToken then return end
            failures = 0
            log.trace("respawn: run %d continues", token)
        end)

        -- Armed for the run. The bait deliberately takes a hit and a guard
        -- can land another on arrival; without this the character dies and the
        -- cycle is over. Disarmed in stop(), which restores every original
        -- Humanoid value.
        -- ORDER MATTERS. V3.1 swaps the Humanoid FIRST, at the top of
        -- stealLoop, before anything else is armed - the anticheat caches
        -- its reference at CharacterAdded and everything after this point
        -- depends on that reference being dead.
        -- CONTAINED, AND THE WORKER SPAWNS WHATEVER HAPPENS.
        --
        -- These three were called bare. `running` and st.autoStealOn are already
        -- true by this point, so a throw in any of them left the hub in the worst
        -- possible state: isRunning() true, the toggle on, and NO WORKER THREAD -
        -- a run that reports itself as live and never moves the character. The
        -- toggle callback is invoked through the UI library, which swallows the
        -- error, so nothing said so either.
        --
        -- A failed arm is not a reason to refuse to steal. hswap failing means
        -- teleports get punished and the guard failing means a hit can land -
        -- both already log loudly on their own - so they are recorded here and
        -- the run goes ahead rather than dying silently.
        local armed = {}
        for _, a in ipairs({ { "humanoid", hswap.arm }, { "guard", guard.arm },
                             { "antideath", adeath.arm } }) do
            local ok = BX.try("autosteal.arm." .. a[1], a[2])
            armed[#armed + 1] = a[1] .. (ok and "=ok" or "=FAILED")
        end
        log.info("run %d: armed %s", token, table.concat(armed, " "))

        sc:spawn("loop", function()
            log.info("run %d: worker thread started (owner=%s, options: %s)",
                token, tostring(owner),
                opts.uid and ("uid=" .. tostring(opts.uid))
                    or (opts.pick and ("picker" .. (opts.continuous and ", continuous" or ""))
                        or "best value"))
            local reason = runLoop(token)
            -- Through stop(), never a bare break: that is the one path that
            -- destroys the scope, disarms guard/antideath/humanoid and resets
            -- movement. Guarded on the token so a run that was already
            -- replaced cannot stop its successor.
            -- EVERY EXIT GOES THROUGH stop(), NOT JUST A DELIVERY.
            --
            -- This used to fire only for "delivered", so any other way out of
            -- runLoop left `running` and st.autoStealOn true with no worker
            -- thread behind them: the toggle stayed on, isRunning() lied, and the
            -- character never moved again. The MAX_PREPS exit hits exactly that -
            -- reachable in normal play when the egg inventory is full, because the
            -- hand-clearing delivery keeps being refused.
            --
            -- stop() is idempotent, so the ordinary path (the loop ending because
            -- stop() already ran) costs nothing here.
            if token == runToken then
                if reason == "delivered" then
                    stop("delivered")
                elseif running then
                    stop(reason or "ended")
                end
            end
        end)
    end

    -- setOptions("farm", { ... }). A table as the first argument is treated as
    -- Main's, so the original one-argument call still means what it did.
    function M.setOptions(src, o)
        if type(src) == "table" or src == nil then src, o = "main", src end
        src = tostring(src)
        optsFor[src] = o or {}
        -- Live update, but only for the tab that owns the run.
        if running and owner == src then
            opts = optsFor[src]
            log.info("%s updated its options mid-run", src)
        end
    end

    function M.setEnabled(on, src)
        src = tostring(src or "main")
        if on then
            local okStart, why = start(src)
            if okStart == false then return false, why end
        else
            -- A tab may only stop the run it started. Without this, flipping
            -- Farm's toggle off would kill a run Main had started.
            if running and owner ~= nil and owner ~= src then
                log.info("%s asked to stop, but %s owns this run - ignored", src, owner)
                return false
            end
            stop("toggled off")
        end
        return true
    end

    function M.owner() return owner end

    function M.isRunning()
        return running
    end

    function M.runOnce(cancelFn)
        -- One cycle, for testing and for a manual single steal. Uses the same
        -- code path as the loop so a test cannot pass against a different
        -- implementation from the one that ships.
        local before = snapshot()
        local ok, why, cycle = runCycle(runToken, cancelFn or function() return false end)
        local after = snapshot()
        if cycle then reportCycle(ok, why, cycle, before, after) end
        return ok, why, cycle, before, after
    end

    function M.status()
        return {
            running  = running,
            token    = runToken,
            cycles   = cycles,
            failures = failures,
            tier     = dev.tier,
            scope    = sc and sc:counts() or nil,
        }
    end

    -- Unloading the menu, or a re-execute retiring this generation, must take
    -- the run with it rather than leave it going with no way to reach it.
    M.stop = stop

    return M
end)


-- ================= BLYXO 4.1.0 BOSS EVENT MODULES =================
--[[ ==== features/boss.lua =========================================== ]]
-- =============================================================================
-- FEATURES.BOSS: is the boss world open, get in, claim what is owed
-- =============================================================================
--
--     local boss = BX.require("features.boss")
--     boss.setEnabled(true)     -- start watching; nothing runs before this
--     boss.status()             -- { title =, body = } for the Event tab
--     boss.enter()              -- RF/BossEvent/AskEnter
--     boss.setAutoEnter(true)   -- join as soon as it opens
--     boss.claimMilestones()    -- every mastery milestone we have earned
--     boss.onChange(fn)
--
-- WHAT THIS IS A PORT OF.
--
-- V3.1's Event tab Boss section, minus the fight. Its four controls were:
--     "Abyss Overlord" status line, "Enter the boss world",
--     "Auto enter", "Claim mastery rewards"
-- and all four dependencies are still live on the current game - verified, see
-- the port table in ui/tabs/event.lua.
--
-- THE FIGHT IS NOT HERE, AND THAT IS DELIBERATE.
--
-- V3.1's "Auto fight" is roughly 1,500 lines - BX.startBossMover (200 lines on
-- its own), startBossDodge, startVoidWatch, crystal reach and swing-gap tuning
-- measured against the tool's 0.6s debounce. It is a second character-owning
-- automation the size of Auto Steal, and nothing in V4 currently arbitrates two
-- of those against each other. It is left out until that is asked for
-- explicitly rather than half-ported.
--
-- EVENT-DRIVEN, WHERE V3.1 POLLED.
--
-- V3.1 ran a permanent thread at K.BOSS_POLL_FAST = 1s while the window was
-- open and K.BOSS_POLL_IDLE = 10s otherwise, invoking RF/BossEvent/AskSnapshot
-- on every tick for the whole session. The current game publishes
-- RE/BossEvent/StateShifted, so the open/closed transition tells us and the
-- snapshot is only re-read when it changes.
--
-- The countdown still needs a clock, but a countdown is arithmetic on a
-- timestamp we already hold - OpensAt and ClosesAt are absolute server times -
-- so the TAB asks for a formatted string when it repaints. Nothing polls the
-- server to move a number we can compute.
--
-- NOTHING EXISTS WHILE IT IS OFF. No scope, no connection, no snapshot.

BX.module("features.boss", function(BX)
    local svc = BX.require("core.services")
    local dev = BX.require("core.device")
    local net = BX.require("core.net")
    local log = BX.require("boot.log").for_module("boss")

    local M = {}

    local K = {
        -- A held snapshot is good for this long. V3.1's fast poll was 1s; this
        -- is only a floor on how often an explicit ask can hit the server.
        SNAP_TTL  = 5,
        -- The backstop. StateShifted is the real signal; this covers a missed
        -- one. V3.1's idle poll was 10s and ran forever - this runs only while
        -- the section is enabled and does nothing when nothing changed.
        BACKSTOP  = 30,
        -- How long after "it opened" we wait before asking to go in, so the
        -- server has finished setting the world up.
        ENTER_GAP = 1.0,
        -- After a FAILED read: retry this soon, then the next, then hand
        -- over to the backstop. Bounded.
        RETRY     = { 5, 10, 20 },
    }
    M.K = K

    local sc, enabled = nil, false
    local snap, snapAt = nil, 0
    local retryN, retryArmed = 0, false
    local autoEnter = false
    local stats = { asks = 0, enters = 0, entersRefused = 0, claims = 0,
                    stateEvents = 0, autoEntered = 0 }

    function M.stats() return table.clone(stats) end
    function M.isOn() return enabled end
    function M.autoEnterOn() return autoEnter end

    local listeners = {}
    function M.onChange(fn) listeners[#listeners + 1] = fn end
    local function fireChange()
        for _, fn in ipairs(listeners) do
            task.spawn(function() BX.try("boss.onChange", fn) end)
        end
    end

    ---------- the snapshot ----------

    -- RF/BossEvent/AskSnapshot. Verified live; returns
    --     { Open = boolean, OpensAt = number, ClosesAt = number }
    -- with OpensAt/ClosesAt as absolute server times.
    function M.snapshot(force)
        if not enabled then return nil end
        local now = os.clock()
        if not force and snap and (now - snapAt) < K.SNAP_TTL then return snap end
        stats.asks = stats.asks + 1
        local st = net.call("RF/BossEvent/AskSnapshot")
        snapAt = now
        if type(st) == "table" then snap = st end
        return snap
    end

    function M.isOpen()
        local s = M.snapshot()
        return (s and s.Open == true) or false
    end

    -- The held snapshot, no read. For status lines painted on a timer.
    function M.held() return snap end

    ---------- the status line ----------

    local function clock(seconds)
        seconds = math.max(0, math.floor(seconds or 0))
        local h = math.floor(seconds / 3600)
        local m = math.floor(seconds / 60) % 60
        if h > 0 then return ("%dh %02dm"):format(h, m) end
        if m > 0 then return ("%dm %02ds"):format(m, seconds % 60) end
        return ("%ds"):format(seconds)
    end

    -- V3.1's line was titled "Abyss Overlord" and read "Reading..." until it
    -- had an answer. Same shape: a title and one line of body.
    --
    -- HELD DATA ONLY: the Event tab's painter calls this once a second for
    -- the countdown, and the countdown is arithmetic on OpensAt/ClosesAt -
    -- absolute server times - so nothing here may invoke the remote. The
    -- backstop, StateShifted and the retry own every read.
    function M.status()
        if not enabled then return { title = "Abyss Overlord", body = "off" } end
        local s = snap
        if not s then
            return { title = "Abyss Overlord", body = (stats.asks > 0)
                and "Unavailable  \u{B7}  retrying"
                or "Reading..." }
        end

        -- Server time, because OpensAt/ClosesAt are server timestamps.
        local nowSrv = workspace:GetServerTimeNow()
        if s.Open == true then
            local left = (tonumber(s.ClosesAt) or 0) - nowSrv
            return { title = "Abyss Overlord",
                     body = ("OPEN  \u{B7}  closes in %s"):format(clock(left)) }
        end
        local until_ = (tonumber(s.OpensAt) or 0) - nowSrv
        if until_ > 0 then
            return { title = "Abyss Overlord",
                     body = ("Closed  \u{B7}  opens in %s"):format(clock(until_)) }
        end
        return { title = "Abyss Overlord", body = "Closed" }
    end

    -- A forced re-read plus a repaint, off the caller's thread. The Event
    -- tab's Refresh button.
    function M.refresh()
        if not enabled then return false end
        task.spawn(function()
            BX.try("boss.refresh", function()
                M.snapshot(true)
                fireChange()
            end)
        end)
        return true
    end

    -- A FAILED READ IS NOT THE END. V3.1's poll simply asked again next tick;
    -- this asks again at 5s, 10s, 20s and then leaves it to the backstop. One
    -- retry armed at a time.
    local function readOrRetry()
        local st = M.snapshot(true)
        if st then
            retryN = 0
            return st
        end
        if retryArmed or not sc then return nil end
        local wait = K.RETRY[retryN + 1]
        if not wait then return nil end
        retryArmed = true
        log.warn("boss read failed - retrying in %ds", wait)
        sc:delay("retry", dev.scale(wait), function()
            retryArmed = false
            retryN = retryN + 1
            if readOrRetry() then fireChange() end
        end)
        return nil
    end

    ---------- entering ----------

    -- Returns ok, message. V3.1 distinguished three answers and so does this:
    -- accepted, "already defeated" (wait for the next window), and a refusal.
    function M.enter()
        stats.enters = stats.enters + 1
        local accepted, msg = net.call("RF/BossEvent/AskEnter")
        log.info("AskEnter -> accepted=%s msg=%s", tostring(accepted), tostring(msg))
        if accepted == true then
            return true, "Entering the boss world"
        end
        stats.entersRefused = stats.entersRefused + 1
        if msg and tostring(msg):find("defeated") then
            return false, "Boss already defeated - waiting for the next one"
        end
        return false, tostring(msg or "Refused")
    end

    function M.setAutoEnter(on)
        autoEnter = on and true or false
        log.info("auto enter %s", autoEnter and "ON" or "OFF")
        -- If it is already open, act now rather than waiting for the next
        -- StateShifted - which may be half an hour away.
        if autoEnter and enabled and M.isOpen() then
            task.spawn(function()
                BX.try("boss.autoEnterNow", function()
                    local ok, why = M.enter()
                    if ok then stats.autoEntered = stats.autoEntered + 1 end
                    log.info("auto enter (already open) -> %s %s", tostring(ok), tostring(why))
                end)
            end)
        end
        return true
    end

    ---------- mastery ----------

    -- V3.1 BX.claimBossMilestones, traced rather than reinvented: it reads
    -- every milestone id out of Data.BossMastery, adds InfiniteMilestoneId, and
    -- asks for each one, treating "Not enough" as the ordinary not-yet-earned
    -- answer rather than an error. Returns how many were actually claimed.
    function M.claimMilestones()
        local BM
        local okReq = BX.try("boss.requireMastery", function()
            local mod = svc.ReplicatedStorage:FindFirstChild("Data")
            mod = mod and mod:FindFirstChild("BossMastery")
            if mod and mod:IsA("ModuleScript") then BM = require(mod) end
        end)
        if not okReq or type(BM) ~= "table" then
            log.warn("Data.BossMastery unavailable - cannot claim")
            return 0, "Could not read the mastery list"
        end

        local ids = {}
        for _, m in pairs(BM.Milestones or {}) do
            if type(m) == "table" and m.Id then ids[#ids + 1] = tostring(m.Id) end
        end
        if BM.InfiniteMilestoneId then ids[#ids + 1] = tostring(BM.InfiniteMilestoneId) end

        local claimed = 0
        for _, id in ipairs(ids) do
            local got, msg = net.call("RF/BossMastery/AskClaimMilestone", id)
            if got == true then
                claimed = claimed + 1
                log.info("claimed milestone %s", id)
            elseif msg and not tostring(msg):find("Not enough") then
                log.trace("milestone %s -> %s", id, tostring(msg))
            end
            -- V3.1's 0.15s gap: this is a loop of remote calls and it must not
            -- become a burst. Yields unconditionally.
            task.wait(0.15)
        end
        stats.claims = stats.claims + claimed
        return claimed, claimed > 0 and ("Claimed " .. claimed) or "Nothing to claim yet"
    end

    ---------- on / off ----------

    function M.setEnabled(on)
        on = on and true or false
        if on == enabled then return true end

        if not on then
            enabled = false
            autoEnter = false
            if sc then sc:destroy() sc = nil end
            snap, snapAt = nil, 0
            retryN, retryArmed = 0, false
            log.info("off (%d snapshot reads this session)", stats.asks)
            fireChange()
            return true
        end

        sc = BX.scope("features.boss")
        enabled = true

        -- THE OPEN/CLOSE TRANSITION, FROM THE GAME'S OWN EVENT. This is what
        -- replaces V3.1's permanent 1s/10s poll.
        BX.try("boss.watchState", function()
            local re = net.find("RE/BossEvent/StateShifted")
            if not re then
                log.warn("RE/BossEvent/StateShifted not found - running on the backstop")
                return
            end
            sc:connect(re.OnClientEvent, function()
                stats.stateEvents = stats.stateEvents + 1
                task.spawn(function()
                    BX.try("boss.stateShifted", function()
                        local was = snap and snap.Open
                        M.snapshot(true)
                        local isOpen = snap and snap.Open
                        log.info("state shifted: open %s -> %s",
                            tostring(was), tostring(isOpen))
                        fireChange()
                        -- Newly open, and the user asked to be let in.
                        if autoEnter and isOpen == true and was ~= true then
                            task.wait(K.ENTER_GAP)
                            local ok, why = M.enter()
                            if ok then stats.autoEntered = stats.autoEntered + 1 end
                            log.info("auto enter on open -> %s %s",
                                tostring(ok), tostring(why))
                        end
                    end)
                end)
            end)
        end)

        -- THE FIRST READ AND THE BACKSTOP ARE ONE LOOP. sc:loop runs its body
        -- before its first wait, so the t=0 tick is the initial read, on the
        -- loop's own thread - never the caller's, which may be the UI build.
        -- A separate "first" spawn used to double the startup AskSnapshot.
        sc:loop("backstop", dev.scale(K.BACKSTOP), function()
            local had, was = snap ~= nil, snap and snap.Open
            readOrRetry()
            if not had or (snap and snap.Open) ~= was then fireChange() end
        end)

        log.info("on (StateShifted event + %.0fs backstop)", dev.scale(K.BACKSTOP))
        return true
    end

    return M
end)

--[[ ==== features/bossfight.lua ====================================== ]]
-- =============================================================================
-- FEATURES.BOSSFIGHT: V3.1's Auto fight - crystals first, then the hands
-- =============================================================================
--
--     local fight = BX.require("features.bossfight")
--     fight.setEnabled(true)     -- the user's wish; it waits until it can act
--     fight.status()             -- { title =, body = } for the Event tab
--     fight.isOn()
--
-- WHAT THIS IS A PORT OF, LINE FOR LINE WHERE IT MATTERS.
--
-- V3.1 "Auto fight" was four pieces, all started by the toggle and all reading
-- BX.autoBossFight + BX.inBossArena() before doing anything:
--
--     BX.startBossMover     Heartbeat: ONE writer of the character's CFrame.
--                           Walks to BX.bossGoal (or BX.bossDodge), probes for
--                           floor every step, follows the rim of the pit,
--                           sidesteps scenery, cancels flings, lifts us back
--                           onto the floor if noclip let us sink.
--     the fight tick        every K.BOSS_TICK (0.12s): equips the bat, picks
--                           the target (nearest live crystal, then the nearest
--                           reachable hand), hands the mover a goal, faces the
--                           target when in range, swings every 0.65s.
--     BX.startBossDodge     0.08s: publishes an escape when standing in a
--                           hazard. SHIPPED DISABLED (BX.bossDodgeEnabled =
--                           false - "do not run from the thing that chases
--                           you"), so inAnyHazard answered nil and this loop
--                           did nothing. Kept, still off, behind K.DODGE.
--     BX.startVoidWatch     0.2s: pulls us back onto the last solid ground if
--                           the floor probe fails three times AND we are well
--                           below where we stood. Arena half only; the steal
--                           half belongs to the steal.
--
-- Every constant below is V3.1's, with its reasoning kept. Nothing about how
-- the fight chooses, moves or swings is new.
--
-- TRACED FROM THE ROOT V3.1 MONOLITH, NOT V3_RELEASE. The first cut of this
-- file came from V3_RELEASE and missed what V3.1 fixed after the game made
-- the bat a gear item: the IsBat attribute, Humanoid:EquipTool (reparenting
-- never fires Equipped for the bat controller, so bat:Activate() is refused),
-- the swing fired straight at RE/BatSwing/Trigger with the controller's own
-- payload, CooldownActive, and "no bat must not mean no movement". That was
-- the "Auto fight does nothing" of the second cut.
--
-- WHAT IS DIFFERENT, AND WHY.
--
-- 1. ONE SCOPE. V3.1 had a Heartbeat connection, three threads and a
--    session-long tick that all read a global to know whether to stop. Here
--    setEnabled(false) destroys the scope and everything in it. Re-executing
--    does the same through BX.teardown. Nothing here can outlive the switch.
--
-- 2. ON MEANS "WANTED", NOT "RUNNING". The toggle is the user's preference
--    and stays up. Whether we can actually fight is a separate question -
--    are we in the arena, is the boss there - and status() says which, so the
--    switch never snaps back and never lies. That is the whole bug this file
--    exists to fix: V4 shipped the control with a callback that turned itself
--    off and logged "not ported".
--
-- 3. NOCLIP ONLY INSIDE THE ARENA. V3.1 switched collision off the moment the
--    toggle went on, even standing on the main map with the boss closed. The
--    reason for noclip is the arena's 195 collidable parts; it goes on when
--    the InBossArena attribute does and comes back off when we leave. The
--    steal's own noclip is left alone: features.movement owns it, and it is
--    only released here when Auto Steal is not running.
--
-- 4. AUTO STEAL IS NEVER FOUGHT OVER THE CHARACTER. While autosteal.isRunning()
--    the tick and the mover stand down. In practice you cannot be in the arena
--    and mid-steal at once, so this is a guard, not a policy.

BX.module("features.bossfight", function(BX)
    local svc  = BX.require("core.services")
    local dev  = BX.require("core.device")
    local ch   = BX.require("core.character")
    local net  = BX.require("core.net")
    local boss = BX.require("features.boss")
    local mov  = BX.require("features.movement")
    local auto = BX.require("features.autosteal")
    local log  = BX.require("boot.log").for_module("bossfight")

    local M = {}

    local K = {
        -- THE FIGHT TICK IS NOT THE SWING TICK. V3.1: these were one number
        -- (0.65s) and the character moved for 0.4s then froze for 0.25s.
        TICK            = 0.12,
        -- SWING GAP MUST CLEAR THE TOOL'S OWN 0.6s DEBOUNCE. It was 0.35 and
        -- every other Activate was swallowed before a packet went out.
        SWING_GAP       = 0.65,
        REACH           = 9,
        -- A tool parented this frame has not fired Equipped; the controller
        -- still says _isEquipped = false and swallows the swing.
        EQUIP_SETTLE    = 0.25,
        -- LET A FRESH CHARACTER SETTLE BEFORE THE MOVER TOUCHES IT.
        --
        -- On the arena-entry edge - the enter teleport AND every respawn -
        -- the server is still asserting the character's position for a beat.
        -- If the Heartbeat mover starts writing CFrame on frame 1 of the new
        -- character it fights that assertion: the server Relocates, the mover
        -- writes again, and the crystal is never reached - the "smooth before
        -- death, snapback loop after respawn" report. The first entry looked
        -- fine only because the enter teleport had already placed us; a
        -- respawn has not. Holding the mover for this long lets the new root
        -- settle, exactly as the first entry was already settled.
        RESPAWN_SETTLE  = 0.6,
        -- How far above us a hand may be and still be swung at / walked to.
        HAND_REACH_Y    = 30,
        HAND_CHASE_Y    = 90,
        HAND_RISE_EPS   = 2,      -- upward studs per tick = "this arm is retracting"
        HAND_COMMIT     = 1.5,    -- seconds we stay on a chosen hand
        -- STAND OUTSIDE THE THING. A crystal Hitbox is 55^3; reach is
        -- max(REACH, half + this), i.e. just clear of the surface.
        SURFACE_MARGIN  = -20,
        -- The mover. 420 with a 14-stud frame cap: Relocate is the limit.
        STEP_SPEED      = 420,
        MAX_STEP        = 14,
        MAX_DT          = 0.05,
        SINK_MAX        = 6,      -- below the last real floor = fell through
        Y_TAU           = 0.12,   -- seconds to close ~63% of a height change
        STUCK_TIME      = 2.5,
        RIM_SWEEP       = { 25, 50, 75, 100, 125, 150 },
        RIM_LOOKAHEAD   = 6,
        MOVE_ARRIVE     = 1.5,
        SWING_SLACK     = 4,      -- must exceed MOVE_ARRIVE or they deadlock
        AIM_COS         = 0.906,  -- cos 25 deg
        AIM_EASE        = 0.35,
        TRACK_TAU       = 0.18,   -- low-pass on a hand's jittering position
        TRACK_JUMP      = 60,
        WAIT_MAX        = 2.5,    -- longest we hold for a hazard to clear
        -- Anti-fling: cancel what our own walk could not have produced.
        FLING_UP        = 60,
        FLING_MULT      = 2.0,
        -- Floor probe.
        GROUND_BAND     = 25,
        PROBE_UP        = 40,
        PROBE_DOWN      = 220,
        SOLID_STEPS     = 8,
        IGNORE_TTL      = 0.5,    -- the raycast ignore list is rebuilt this often
        -- Rim walking.
        RING_STEP_DEG   = 22,
        RING_RADII      = { 1.0, 0.85, 1.15, 0.7, 1.3 },
        AROUND_ANGLES   = { 25, 45, 70, 95, 120, 145 },
        AROUND_FRAC     = 0.55,
        AROUND_MIN_R    = 90,
        -- Hazards.
        HAZARD_CACHE    = 0.1,
        HAZARD_CLEAR    = 6,
        SLAM_CLEAR      = 12,
        RING_CLEAR      = 2,      -- the red rings are 2 studs wide, not walls
        HOLE_CLEAR      = 6,
        DODGE_GAP       = 0.08,
        DODGE_POINTS    = 16,
        DODGE           = false,  -- V3.1 BX.bossDodgeEnabled. Shipped off.
        ORBIT_TRIGGER   = 34,
        ORBIT_STEP      = 0.55,
        -- Void watch (arena half).
        VOID_GAP        = 0.2,
        VOID_MISSES     = 3,
        VOID_DROP_PROOF = 25,
        MAX_RISE        = 8,
        HAND_BONES      = { "UpperHand1.R", "UpperHand1.L", "LowerHand1.R", "LowerHand1.L" },
    }
    M.K = K

    local sc, enabled = nil, false
    local stats = { swings = 0, dodges = 0, flings = 0, voidSaves = 0, rescues = 0, kills = 0 }
    function M.stats() return table.clone(stats) end
    function M.isOn() return enabled end

    -- Everything V3.1 kept as BX.* globals, in one table that setEnabled(false)
    -- drops. Names match V3.1's so the two can be read side by side.
    local S = nil
    local function fresh()
        return {
            goal = nil, dodge = nil, aim = nil, trackPos = nil,
            handY = {}, handPick = nil, handPickAt = 0,
            lastSolid = nil, arenaFloorY = nil,
            stuckBest = nil, stuckSince = nil, stuckFlip = false, rimSide = 1,
            lastSwingAt = 0, batFor = nil, waitAt = nil, idlePhase = false,
            arena = nil, hazards = nil, hazardsAt = 0,
            ignore = nil, ignoreAt = 0,
            inArena = false, noclipped = false, left = false,
            settleUntil = 0, batAskedAt = 0,
            voidAnchor = nil, voidMisses = 0,
            lastLog = {},
            phase = nil, kind = nil,
        }
    end

    -- Rate-limited trace lines, keyed, so the log reads like V3.1's.
    local function every(key, secs, fmt, ...)
        local now = os.clock()
        if now - (S.lastLog[key] or 0) < secs then return end
        S.lastLog[key] = now
        log.info(fmt, ...)
    end

    ---------- the arena ----------

    local function inArena()
        return svc.LocalPlayer:GetAttribute("InBossArena") == true
    end
    M.inArena = inArena

    -- Cached: a recursive FindFirstChild walks 38k instances and the tick
    -- asks eight times a second.
    local function arena()
        local a = S.arena
        if a and a.Parent then return a end
        a = workspace:FindFirstChild("BossArena") or workspace:FindFirstChild("BossArena", true)
        S.arena = a
        return a
    end

    local function arenaFloor()
        local a = arena()
        local f = a and a:FindFirstChild("Floor", true)
        if f and f:IsA("BasePart") then return f end
        return nil
    end

    local function arenaCentre()
        local f = arenaFloor()
        if f then return f.Position end
        local a = arena()
        if a and a.PrimaryPart then return a.PrimaryPart.Position end
        return nil
    end

    local function bossModel()
        local a = arena()
        if not a then return nil end
        local b = a:FindFirstChild("Boss", true)
        if b and b:IsA("Model") then return b end
        return nil
    end

    -- nil while the boss is still arriving, "crystals" or "hands" otherwise.
    local function phase()
        local b = bossModel()
        if not b then return nil end
        if b:GetAttribute("Spawning") then return nil end
        if b:GetAttribute("PhaseTwoAt") ~= nil then return "hands" end
        return "crystals"
    end

    ---------- the floor ----------

    -- IS THERE ACTUALLY GROUND THERE. V3.1 probed the floor on a 13x13 grid:
    -- 51 of 169 cells had nothing under them, the centre included - the Floor
    -- part's box says "floor" for a pit. A downward ray is the only thing that
    -- knows the difference. Crystal towers, the boss and the hazards are
    -- ignored so a ray beside a tower does not report its top as ground.
    local probeParams = RaycastParams.new()
    probeParams.FilterType = Enum.RaycastFilterType.Exclude
    probeParams.IgnoreWater = true

    local function refreshIgnore()
        local now = os.clock()
        if S.ignore and (now - S.ignoreAt) < K.IGNORE_TTL then return end
        local ignore = {}
        for _, pl in ipairs(svc.Players:GetPlayers()) do
            if pl.Character then ignore[#ignore + 1] = pl.Character end
        end
        local a = arena()
        if a then
            for _, nm in ipairs({ "CrystalTowers", "Boss", "SlamIndicator",
                                  "SlamArmHitbox", "SlamRestHitbox" }) do
                local d = a:FindFirstChild(nm, true)
                if d then ignore[#ignore + 1] = d end
            end
        end
        for _, nm in ipairs({ "BossHazards", "BossBlackHole" }) do
            local d = workspace:FindFirstChild(nm)
            if d then ignore[#ignore + 1] = d end
        end
        probeParams.FilterDescendantsInstances = ignore
        S.ignore, S.ignoreAt = ignore, now
    end

    local function groundAt(pos)
        refreshIgnore()
        -- Anchored to the arena floor, not to us: once we are falling a ray
        -- from above our head starts under the floor and never answers.
        local top = pos.Y + K.PROBE_UP
        local f = arenaFloor()
        if f then top = math.max(top, f.Position.Y + K.PROBE_UP) end
        local reach = math.max(K.PROBE_DOWN, (top - pos.Y) + K.PROBE_DOWN)
        local r = workspace:Raycast(Vector3.new(pos.X, top, pos.Z),
                                    Vector3.new(0, -reach, 0), probeParams)
        if not r then return nil end
        if f and (r.Position.Y - f.Position.Y) > K.GROUND_BAND then return nil end
        return r.Position.Y
    end

    local function onFloor(pos) return groundAt(pos) ~= nil end

    local function lastSolidToward(from, to)
        local flat = Vector3.new(to.X - from.X, 0, to.Z - from.Z)
        local dist = flat.Magnitude
        if dist < 1 then return nil end
        local dir = flat.Unit
        local best
        -- At most SOLID_STEPS probes, by construction: a bounded for, not a
        -- while, so a bad step size can never spin.
        local step = math.max(dist / K.SOLID_STEPS, 20)
        for i = 1, K.SOLID_STEPS do
            local d = step * i
            if d > dist then break end
            local p = from + dir * d
            local gy = groundAt(Vector3.new(p.X, from.Y, p.Z))
            if not gy then break end
            best = Vector3.new(p.X, gy, p.Z)
        end
        return best
    end

    local function clearLine(a, b)
        local flat = Vector3.new(b.X - a.X, 0, b.Z - a.Z)
        local dist = flat.Magnitude
        if dist < 1 then return true end
        local dir = flat.Unit
        local step = math.max(dist / K.SOLID_STEPS, 20)
        for i = 1, K.SOLID_STEPS do
            local d = step * i
            if d >= dist then break end
            local p = a + dir * d
            if not groundAt(Vector3.new(p.X, a.Y, p.Z)) then return false end
        end
        return true
    end

    -- WALK THE RING. The floor is an annulus; step our bearing round the
    -- centre toward the target's bearing, holding our radius.
    local function ringWaypoint(from, to)
        local mid = arenaCentre()
        if not mid then return nil end
        local a = Vector3.new(from.X - mid.X, 0, from.Z - mid.Z)
        local b = Vector3.new(to.X - mid.X, 0, to.Z - mid.Z)
        if a.Magnitude < 20 or b.Magnitude < 20 then return nil end
        local ang1, ang2 = math.atan2(a.Z, a.X), math.atan2(b.Z, b.X)
        local diff = ang2 - ang1
        while diff > math.pi do diff = diff - 2 * math.pi end
        while diff < -math.pi do diff = diff + 2 * math.pi end
        local step = math.min(math.abs(diff), math.rad(K.RING_STEP_DEG))
        if diff < 0 then step = -step end
        local want = ang1 + step
        for _, mul in ipairs(K.RING_RADII) do
            local r = a.Magnitude * mul
            local p = Vector3.new(mid.X + math.cos(want) * r, from.Y, mid.Z + math.sin(want) * r)
            local gy = groundAt(p)
            if gy then
                local wp = Vector3.new(p.X, gy, p.Z)
                if clearLine(from, wp) then return wp, math.deg(step) end
            end
        end
        return nil
    end

    local function rotated(dir, a)
        return Vector3.new(dir.X * math.cos(a) - dir.Z * math.sin(a), 0,
                           dir.X * math.sin(a) + dir.Z * math.cos(a))
    end

    local function detourAround(from, to)
        if clearLine(from, to) then return nil end
        local flat = Vector3.new(to.X - from.X, 0, to.Z - from.Z)
        local dist = flat.Magnitude
        if dist < 1 then return nil end
        local dir = flat.Unit
        local r = math.max(dist * K.AROUND_FRAC, K.AROUND_MIN_R)
        for _, deg in ipairs(K.AROUND_ANGLES) do
            for _, sign in ipairs({ 1, -1 }) do
                local wp = from + rotated(dir, math.rad(deg) * sign) * r
                local gy = groundAt(Vector3.new(wp.X, from.Y, wp.Z))
                if gy then
                    wp = Vector3.new(wp.X, gy, wp.Z)
                    if clearLine(from, wp) and clearLine(wp, to) then return wp, deg * sign end
                end
            end
        end
        for _, deg in ipairs(K.AROUND_ANGLES) do
            for _, sign in ipairs({ 1, -1 }) do
                local wp = from + rotated(dir, math.rad(deg) * sign) * r
                local gy = groundAt(Vector3.new(wp.X, from.Y, wp.Z))
                if gy and clearLine(from, Vector3.new(wp.X, gy, wp.Z)) then
                    return Vector3.new(wp.X, gy, wp.Z), deg * sign
                end
            end
        end
        return nil
    end

    ---------- hazards ----------

    local function hazardParts()
        local now = os.clock()
        if S.hazards and (now - S.hazardsAt) < K.HAZARD_CACHE then return S.hazards end
        local out = {}
        local folder = workspace:FindFirstChild("BossHazards")
        if folder then
            for _, d in ipairs(folder:GetDescendants()) do
                if d:IsA("BasePart") then out[#out + 1] = d end
            end
        end
        local a = arena()
        if a then
            for _, name in ipairs({ "SlamIndicator", "SlamArmHitbox", "SlamRestHitbox" }) do
                local d = a:FindFirstChild(name)
                if d and d:IsA("BasePart") then out[#out + 1] = d end
            end
        end
        local bh = workspace:FindFirstChild("BossBlackHole")
        if bh and bh:IsA("BasePart") then out[#out + 1] = bh end
        S.hazards, S.hazardsAt = out, now
        return out
    end

    local function hazardClear(part)
        local n = part.Name
        if n == "BossBlackHole" then return K.HOLE_CLEAR end
        if n:find("Slam") then return K.SLAM_CLEAR end
        if n:find("Ring") then return K.RING_CLEAR end
        return K.HAZARD_CLEAR
    end

    local function inHazard(part, pos, extra)
        local clear = hazardClear(part) + (extra or 0)
        if part:IsA("Part") and part.Shape == Enum.PartType.Cylinder then
            local flat = Vector3.new(pos.X - part.Position.X, 0, pos.Z - part.Position.Z)
            return flat.Magnitude <= part.Size.Y * 0.5 + clear
        end
        local rel = part.CFrame:PointToObjectSpace(pos)
        local half = part.Size * 0.5
        return math.abs(rel.X) <= half.X + clear
            and math.abs(rel.Z) <= half.Z + clear
            and math.abs(rel.Y) <= half.Y + 8
    end

    -- With dodging off nothing may refuse a move on account of a hazard
    -- either: this gate stopped V3.1 reaching the crystal until it was tied
    -- to the same switch.
    local function inAnyHazard(pos, extra)
        if not K.DODGE then return nil end
        for _, part in ipairs(hazardParts()) do
            if inHazard(part, pos, extra) then return part end
        end
        return nil
    end

    local function dodgeScore(spot, here)
        local aim = S.aim
        if typeof(aim) == "Vector3" then
            return Vector3.new(spot.X - aim.X, 0, spot.Z - aim.Z).Magnitude
        end
        return Vector3.new(spot.X - here.X, 0, spot.Z - here.Z).Magnitude
    end

    -- Publishes S.dodge for the mover; never moves the character itself.
    local function dodgeHazards()
        if not K.DODGE then S.dodge = nil return false end
        local h = ch.root()
        if not h then return false end
        local parts = hazardParts()
        if #parts == 0 then S.dodge = nil return false end
        local hit = nil
        for _, part in ipairs(parts) do
            if inHazard(part, h.Position) then hit = part break end
        end
        if not hit then S.dodge = nil return false end

        local here = h.Position
        local cands = {}
        if hit:IsA("Part") and hit.Shape == Enum.PartType.Cylinder then
            local want = hit.Size.Y * 0.5 + K.HOLE_CLEAR + 4
            for i = 0, K.DODGE_POINTS - 1 do
                local ang = (2 * math.pi / K.DODGE_POINTS) * i
                cands[#cands + 1] = Vector3.new(hit.Position.X + math.cos(ang) * want, here.Y,
                                                hit.Position.Z + math.sin(ang) * want)
            end
        else
            local rel = hit.CFrame:PointToObjectSpace(here)
            local half = hit.Size * 0.5
            local clear = hazardClear(hit) + 4
            local outX = (rel.X >= 0 and 1 or -1) * (half.X + clear)
            local outZ = (rel.Z >= 0 and 1 or -1) * (half.Z + clear)
            local cf = hit.CFrame
            cands[#cands + 1] = cf:PointToWorldSpace(Vector3.new(rel.X, rel.Y, outZ))
            cands[#cands + 1] = cf:PointToWorldSpace(Vector3.new(outX, rel.Y, rel.Z))
            cands[#cands + 1] = cf:PointToWorldSpace(Vector3.new(rel.X, rel.Y, -outZ))
            cands[#cands + 1] = cf:PointToWorldSpace(Vector3.new(-outX, rel.Y, rel.Z))
            cands[#cands + 1] = cf:PointToWorldSpace(Vector3.new(outX, rel.Y, outZ))
            cands[#cands + 1] = cf:PointToWorldSpace(Vector3.new(-outX, rel.Y, outZ))
        end

        local best, bestScore
        for _, spot in ipairs(cands) do
            if onFloor(spot) and not inAnyHazard(spot, 0) then
                local scr = dodgeScore(spot, here)
                if not bestScore or scr < bestScore then best, bestScore = spot, scr end
            end
        end
        if not best then
            for _, spot in ipairs(cands) do
                if onFloor(spot) then
                    local scr = dodgeScore(spot, here)
                    if not bestScore or scr < bestScore then best, bestScore = spot, scr end
                end
            end
        end
        if not best then
            local mid = arenaCentre()
            if mid then
                local inward = Vector3.new(mid.X - here.X, 0, mid.Z - here.Z)
                if inward.Magnitude > 1 then
                    best = here + inward.Unit * math.min(inward.Magnitude, 60)
                end
            end
        end
        if not best then return true end
        S.dodge = { pos = best }
        stats.dodges = stats.dodges + 1
        return true
    end

    -- ORBIT, DO NOT RETREAT. The black hole follows at 40% of our speed; slide
    -- tangentially round the target at swing radius and it trails behind.
    local function orbitPoint(tpos, reach)
        local h = ch.root()
        local bh = workspace:FindFirstChild("BossBlackHole")
        if not h or not bh or not bh:IsA("BasePart") then return nil end
        local toHole = Vector3.new(bh.Position.X - h.Position.X, 0, bh.Position.Z - h.Position.Z)
        if toHole.Magnitude > K.ORBIT_TRIGGER then return nil end
        local rel = Vector3.new(h.Position.X - tpos.X, 0, h.Position.Z - tpos.Z)
        if rel.Magnitude < 1 then rel = Vector3.new(1, 0, 0) end
        local ang = math.atan2(rel.Z, rel.X)
        local r = math.max(reach, 6)
        local function at(a)
            return Vector3.new(tpos.X + math.cos(a) * r, h.Position.Y, tpos.Z + math.sin(a) * r)
        end
        local p1, p2 = at(ang + K.ORBIT_STEP), at(ang - K.ORBIT_STEP)
        local function fromHole(p)
            return Vector3.new(p.X - bh.Position.X, 0, p.Z - bh.Position.Z).Magnitude
        end
        local first, second = p1, p2
        if fromHole(p2) > fromHole(p1) then first, second = p2, p1 end
        if onFloor(first) then return first end
        if onFloor(second) then return second end
        return nil
    end

    ---------- the character ----------

    -- A bat is a Tool the game marks IsBat (the gear item is "Bat [X1]" now);
    -- the name test stays as a fallback for older builds. V3.1 monolith
    -- BX.isBatTool - V3_RELEASE only had the name test.
    local function isBatTool(t)
        return t:IsA("Tool") and (t:GetAttribute("IsBat") == true or t.Name:find("Bat") ~= nil)
    end

    local function equipBat()
        local char = ch.get()
        if not char then return nil end
        for _, t in ipairs(char:GetChildren()) do
            if isBatTool(t) then return t end
        end
        local bp = svc.LocalPlayer:FindFirstChild("Backpack")
        if bp then
            for _, t in ipairs(bp:GetChildren()) do
                if isBatTool(t) then
                    -- EQUIP IT THE WAY THE GAME DOES. Reparenting the tool from
                    -- an executor does not reliably fire Tool.Equipped for the
                    -- bat's own controller, and that controller refuses every
                    -- swing it did not see equipped (CLIENT_TOOL_NOT_EQUIPPED)
                    -- - the "auto equip does nothing". Humanoid:EquipTool is
                    -- the real path; reparenting stays only as the fallback.
                    local hum = ch.humanoid()
                    local ok = hum and pcall(function() hum:EquipTool(t) end)
                    if not ok or t.Parent ~= char then t.Parent = char end
                    log.info("equipped %s", t.Name)
                    return t
                end
            end
        end
        return nil
    end

    -- ONE SWING, STRAIGHT TO THE SERVER. V3.1 monolith BX.batSwing.
    --
    -- Read out of Shared.Modules.BatController.Client: a click runs
    --     Tool.Activated -> _onActivated -> (equipped? gameplay area? 0.6s
    --     debounce? not ragdolled?) -> Remotes.BatSwing.Trigger:FireServer(
    --         target, "<UserId>:<seq>:<serverTimeMs>")
    -- where `target` is the nearest PLAYER in range and nil otherwise - the
    -- boss and crystals are resolved by the server from where we stand.
    -- bat:Activate() only works if the controller saw the tool equipped, which
    -- on some executors it never does: "I have to click to attack". So the
    -- swing is fired exactly as the controller fires it, and the bat's own hit
    -- animation is played so it still looks like a swing.
    local batSeq, batAnimTrack, batAnimFor = 0, nil, nil
    local function batSwing(bat)
        local ok = pcall(function()
            local rem = net.find("RE/BatSwing/Trigger")
            assert(rem, "no BatSwing remote")
            batSeq = batSeq + 1
            rem:FireServer(nil, ("%d:%d:%d"):format(svc.LocalPlayer.UserId, batSeq,
                math.floor(workspace:GetServerTimeNow() * 1000)))
        end)
        if not ok then
            pcall(function() bat:Activate() end)
            return
        end
        pcall(function()
            local anim = bat:FindFirstChild("HitAnim")
            local hum = ch.humanoid()
            local animator = hum and hum:FindFirstChildOfClass("Animator")
            if anim and animator then
                if batAnimFor ~= animator then
                    batAnimTrack = animator:LoadAnimation(anim)
                    batAnimFor = animator
                end
                batAnimTrack:Play()
            end
            local snd = bat:FindFirstChild("Slash", true)
            if snd and snd:IsA("Sound") then snd:Play() end
        end)
    end

    -- Put the humanoid back in a state that can act. V3.1 readyAfterRagdoll.
    local function readyAfterRagdoll()
        local hm, h = ch.humanoid(), ch.root()
        if not hm or not h then return end
        hm.PlatformStand = false
        hm.Sit = false
        hm.AutoRotate = true
        local st = hm:GetState()
        if st == Enum.HumanoidStateType.Physics
           or st == Enum.HumanoidStateType.PlatformStanding
           or st == Enum.HumanoidStateType.FallingDown
           or st == Enum.HumanoidStateType.Ragdoll
           or st == Enum.HumanoidStateType.Seated then
            hm:ChangeState(Enum.HumanoidStateType.GettingUp)
        end
        h.AssemblyLinearVelocity = Vector3.zero
        h.AssemblyAngularVelocity = Vector3.zero
    end

    -- The slam and the black hole knock with an impulse that is integrated
    -- inside one physics step. Remove only what our own walk could not have
    -- produced; leave downward motion so gravity and the void watch work.
    local function antiFling()
        local h, hum = ch.root(), ch.humanoid()
        if not h or not hum then return end
        local v = h.AssemblyLinearVelocity
        local flat = (v * Vector3.new(1, 0, 1)).Magnitude
        local cap = math.max((hum.WalkSpeed or 16) * K.FLING_MULT, 120)
        if v.Y <= K.FLING_UP and flat <= cap then return end
        local keep = Vector3.zero
        if flat > 0.001 then
            keep = (v * Vector3.new(1, 0, 1)).Unit * math.min(flat, hum.WalkSpeed or 16)
        end
        h.AssemblyLinearVelocity = Vector3.new(keep.X, math.min(v.Y, 0), keep.Z)
        h.AssemblyAngularVelocity = Vector3.zero
        stats.flings = stats.flings + 1
        every("fling", 2, "cancelled a launch (up %.0f, flat %.0f) - %d so far",
            v.Y, flat, stats.flings)
    end

    local function targetReach(part)
        if typeof(part) == "Vector3" then return K.REACH end
        if not (part and part:IsA("BasePart")) then return K.REACH end
        local half = math.max(part.Size.X, part.Size.Z) * 0.5
        return math.max(K.REACH, half + K.SURFACE_MARGIN)
    end

    ---------- the target ----------

    -- Returns target, kind: a BasePart for a crystal, a Vector3 for a hand.
    -- nil when there is genuinely nothing to hit, and the caller HOLDS.
    local function target()
        local h = ch.root()
        if not h then return nil end
        local ph = phase()
        if not ph then return nil end

        if ph == "crystals" then
            local a = arena()
            local towers = a and a:FindFirstChild("CrystalTowers", true)
            if not towers then return nil end
            local best, bestD
            for _, d in ipairs(towers:GetDescendants()) do
                if d:IsA("BasePart") and d.Name == "Hitbox" then
                    -- The game's own test: a missing Health attribute means the
                    -- tower is not live yet, NOT that it is full health.
                    local hp = d:GetAttribute("Health")
                    if type(hp) == "number" and hp > 0 then
                        local dist = (d.Position - h.Position).Magnitude
                        if not bestD or dist < bestD then best, bestD = d, dist end
                    end
                end
            end
            if best then return best, "crystal" end
            return nil
        end

        -- PHASE TWO: THE HANDS, AND ONLY THE HANDS. No health-bar gate: measured
        -- live, no bone ever carries a Health child while BossArmHits still
        -- climbed. Proximity is the signal.
        local b = bossModel()
        if not b then return nil end
        local myY = h.Position.Y
        local low, lowD, any, anyD, anyUp
        for _, bn in ipairs(K.HAND_BONES) do
            local bone = b:FindFirstChild(bn, true)
            if bone and bone:IsA("Bone") then
                local pos
                pcall(function() pos = bone.TransformedWorldCFrame.Position end)
                pos = pos or bone.WorldPosition
                if pos then
                    -- DO NOT CHASE AN ARM THAT IS LEAVING: it retracts to the
                    -- body, and the body sits over the pit.
                    local prev = S.handY[bn]
                    S.handY[bn] = pos.Y
                    local rising = prev ~= nil and (pos.Y - prev) > K.HAND_RISE_EPS
                    if not rising then
                        local flat = Vector3.new(pos.X - h.Position.X, 0, pos.Z - h.Position.Z).Magnitude
                        if not anyD or flat < anyD then any, anyD, anyUp = pos, flat, pos.Y - myY end
                        if (pos.Y - myY) <= K.HAND_REACH_Y and (not lowD or flat < lowD) then
                            low, lowD = pos, flat
                        end
                    end
                end
            end
        end

        -- STAND WHERE THERE IS FLOOR, EVEN IF THE HAND IS NOT OVER ANY.
        local function landable(p)
            if not p then return nil end
            if onFloor(p) and clearLine(h.Position, p) then return p end
            local wp, ang = ringWaypoint(h.Position, p)
            if not wp then wp, ang = detourAround(h.Position, p) end
            if wp then
                every("pit", 2, "pit in the way - walking round the ring (%+.0f deg)", ang or 0)
                return wp
            end
            return lastSolidToward(h.Position, p)
        end

        low = landable(low)
        if any and (anyUp or 0) <= K.HAND_CHASE_Y then any = landable(any) else any = nil end

        -- HOLD THE HAND WE PICKED, or the walk wanders between arms.
        local now = os.clock()
        if S.handPick and (now - S.handPickAt) < K.HAND_COMMIT then
            local keep = S.handPick
            if (low and (low - keep).Magnitude < 220) or (any and (any - keep).Magnitude < 220) then
                return keep, "hand"
            end
        end
        if low then
            S.handPick, S.handPickAt = low, now
            return low, "hand"
        end
        if any then
            S.handPick, S.handPickAt = any, now
            return any, "hand"
        end
        if anyUp then
            every("high", 2, "hands up: nearest is %.0f studs up (need <= %d) - holding for the slam",
                anyUp, K.HAND_REACH_Y)
        end
        return nil
    end

    ---------- leaving ----------

    local function leaveArena()
        local a = arena()
        local exit = a and a:FindFirstChild("BossArenaLeaveTeleport", true)
        local part = exit and (exit:IsA("BasePart") and exit
            or exit:FindFirstChild("Hitbox", true)
            or exit:FindFirstChildWhichIsA("BasePart", true))
        local c = ch.get()
        if not (part and c) then return false end
        c:MoveTo(part.Position + Vector3.new(0, 3, 0))
        return true
    end

    ---------- noclip, arena only ----------

    local function setNoclip(on)
        if on == S.noclipped then return end
        S.noclipped = on
        if on then
            mov.noclip(true)
        elseif not auto.isRunning() then
            -- The steal owns its own noclip while it runs; never pull it out
            -- from under a carry.
            mov.noclip(false)
        end
    end

    ---------- THE MOVER: one writer of the character's CFrame ----------

    local function moverStep(dt)
        if not S.inArena or auto.isRunning() then return end
        -- Do not write CFrame onto a character the server is still spawning:
        -- that race is the post-respawn snapback. Anti-fling still runs below
        -- once settled; during the hold the server owns the position.
        if S.settleUntil and os.clock() < S.settleUntil then return end
        antiFling()

        local dodging = S.dodge ~= nil
        local goal = S.dodge or S.goal
        if not goal then return end
        local h, hum = ch.root(), ch.humanoid()
        if not h or not hum then return end

        -- DEAD MAN'S GUARD: no ground under us outranks every other goal.
        if groundAt(h.Position) then
            S.lastSolid = h.Position
        elseif S.lastSolid then
            local back = Vector3.new(S.lastSolid.X - h.Position.X, 0, S.lastSolid.Z - h.Position.Z)
            if back.Magnitude > 1 then
                local st2 = math.min(back.Magnitude, math.min(dt, K.MAX_DT) * K.STEP_SPEED, K.MAX_STEP)
                local nb = h.Position + back.Unit * st2
                local gyb = groundAt(nb) or S.lastSolid.Y
                hum.PlatformStand = false
                h.CFrame = CFrame.lookAt(Vector3.new(nb.X, gyb, nb.Z), Vector3.new(nb.X, gyb, nb.Z) + back.Unit)
                h.AssemblyLinearVelocity = Vector3.zero
                stats.rescues = stats.rescues + 1
                every("rescue", 1, "no ground underneath - walking back to solid")
            end
            return
        end

        local flat = Vector3.new(goal.pos.X - h.Position.X, 0, goal.pos.Z - h.Position.Z)
        local reach = dodging and 0 or (goal.reach or K.REACH)
        local left = flat.Magnitude - reach
        if left <= K.MOVE_ARRIVE then
            if dodging then S.dodge = nil else S.goal = nil end
            return
        end

        -- STUCK ON SCENERY: watch progress, not the step.
        local now = os.clock()
        if not S.stuckBest or left < S.stuckBest - 2 then S.stuckBest, S.stuckSince = left, now end
        local dirUse = flat.Unit
        if S.stuckSince and (now - S.stuckSince) > K.STUCK_TIME then
            S.stuckFlip = not S.stuckFlip
            local sgn = S.stuckFlip and 1 or -1
            dirUse = Vector3.new(-flat.Unit.Z * sgn, 0, flat.Unit.X * sgn)
            S.stuckSince, S.stuckBest = now, nil
            every("stuck", 2, "not making progress - sidestepping")
        end

        local step = math.min(left, math.min(dt, K.MAX_DT) * K.STEP_SPEED, K.MAX_STEP)
        local nxt = h.Position + dirUse * step
        if not dodging and inAnyHazard(nxt, 0) then return end

        local function groundFor(dir, dist)
            local probe = h.Position + dir * dist
            return groundAt(Vector3.new(probe.X, h.Position.Y, probe.Z))
        end
        local gy = groundFor(dirUse, step)

        -- NOCLIP MEANS THE FLOOR CANNOT CATCH US EITHER. Remember the last
        -- real floor and refuse to write below it.
        if gy then
            S.arenaFloorY = gy
        elseif S.arenaFloorY and h.Position.Y < S.arenaFloorY - K.SINK_MAX then
            h.CFrame = CFrame.new(h.Position.X, S.arenaFloorY, h.Position.Z)
            h.AssemblyLinearVelocity = Vector3.zero
            every("sink", 2, "dropped below the floor - lifted back onto it")
            return
        end

        if not gy then
            -- NO GROUND THERE = DO NOT GO THERE, BUT DO NOT GIVE UP: follow the
            -- rim round until the way ahead is solid again.
            local found = nil
            for _, deg in ipairs(K.RIM_SWEEP) do
                for _, sgn in ipairs(S.rimSide == -1 and { -1, 1 } or { 1, -1 }) do
                    local d = rotated(dirUse, math.rad(deg * sgn))
                    local g = groundFor(d, step)
                    if g and groundFor(d, step + K.RIM_LOOKAHEAD) then
                        found, gy = d, g
                        S.rimSide = sgn
                        break
                    end
                end
                if found then break end
            end
            if not found then
                if dodging then S.dodge = nil else S.goal = nil end
                return
            end
            dirUse = found
            nxt = h.Position + dirUse * step
            S.stuckSince = now
            every("rim", 2, "hole in the way - following the rim round")
        end

        -- EASE THE HEIGHT, FRAME-RATE INDEPENDENTLY.
        local curY = h.Position.Y
        local k = 1 - math.exp(-dt / K.Y_TAU)
        local dest = Vector3.new(nxt.X, curY + (gy - curY) * k, nxt.Z)
        hum.PlatformStand = false
        hum:Move(Vector3.zero, false)
        h.CFrame = CFrame.lookAt(dest, dest + flat.Unit)
        h.AssemblyLinearVelocity = Vector3.new(0, h.AssemblyLinearVelocity.Y, 0)
        h.AssemblyAngularVelocity = Vector3.zero
    end

    ---------- THE FIGHT TICK ----------

    local function fightTick()
        -- Arena entry/exit edge: noclip on inside, off outside; a fresh
        -- fight's bookkeeping each time we go in.
        local inside = inArena()
        if inside ~= S.inArena then
            S.inArena = inside
            setNoclip(inside)
            S.goal, S.dodge, S.aim, S.trackPos, S.handPick = nil, nil, nil, nil, nil
            S.lastSolid, S.arenaFloorY, S.left = nil, nil, false
            S.voidAnchor, S.voidMisses = nil, 0
            if inside then
                -- Fresh (or freshly re-entered) character: put the humanoid in
                -- a movable state and hold the mover until the server has
                -- stopped positioning it. See K.RESPAWN_SETTLE.
                S.settleUntil = os.clock() + K.RESPAWN_SETTLE
                readyAfterRagdoll()
            end
            log.info(inside and "in the arena - fighting" or "left the arena")
        end
        if not inside or auto.isRunning() or S.left then return end
        -- The character is still settling after entry/respawn: no target, no
        -- goal, no CFrame writes yet.
        if S.settleUntil and os.clock() < S.settleUntil then return end

        -- NO BAT MUST NOT MEAN NO MOVEMENT. V3_RELEASE returned here without
        -- a bat - before the target was picked and before the goal was set -
        -- so the character stood still in the arena. The bat is a gear item
        -- with a use count now ("Bat [X1]", Uses=1, CooldownActive), so it can
        -- be used up or simply not there. The fight carries on without one -
        -- moving, targeting - and only the swing waits for a bat. The game
        -- added RF/Codex/AskWearFieldBat alongside that change; ask it for
        -- the arena bat now and then, and log the answer.
        local bat = equipBat()
        if not bat then
            if os.clock() - (S.batAskedAt or 0) > 5 then
                S.batAskedAt = os.clock()
                local okW, msgW = net.call("RF/Codex/AskWearFieldBat")
                log.info("no bat - AskWearFieldBat -> %s %s", tostring(okW), tostring(msgW or ""))
            end
        elseif S.batFor ~= bat then
            S.batFor = bat
            task.wait(K.EQUIP_SETTLE)
        end

        -- Physics state = IsRagdolled = swing refused. None turns up in the
        -- arena too and is not a state the humanoid can act from.
        local hmz = ch.humanoid()
        if hmz then
            local stt = hmz:GetState()
            if hmz.PlatformStand or stt == Enum.HumanoidStateType.Physics
               or stt == Enum.HumanoidStateType.PlatformStanding
               or stt == Enum.HumanoidStateType.None then
                readyAfterRagdoll()
            end
        end

        local inHaz = dodgeHazards()

        -- Dead boss: claim and get out. Then stand down until we leave.
        local snap = boss.snapshot()
        if snap and tonumber(snap.BossHealth) and snap.BossHealth <= 0 then
            stats.kills = stats.kills + 1
            log.info("boss dead - claiming and walking out")
            local n = boss.claimMilestones()
            log.info("claimed %d milestone(s)", n)
            S.left = leaveArena()
            S.goal, S.aim = nil, nil
            return
        end

        S.phase = phase()
        local part, kind = target()
        if not part then
            S.goal, S.aim, S.kind = nil, nil, nil
            if S.idlePhase ~= S.phase then
                S.idlePhase = S.phase
                log.info("nothing to hit (phase=%s) - holding position", tostring(S.phase or "spawning"))
            end
            return
        end
        S.idlePhase, S.kind = false, kind

        local tpos = (typeof(part) == "Vector3") and part or part.Position
        local h = ch.root()
        if not h then return end
        local reach = targetReach(part)

        -- FLAT DISTANCE, NOT 3D: a crystal's hitbox sits 27 studs up.
        local flatDir = Vector3.new(tpos.X - h.Position.X, 0, tpos.Z - h.Position.Z)
        local d = flatDir.Magnitude

        -- DO NOT WALK INTO THE SLAM TO REACH SOMETHING - but not forever.
        local stand = h.Position + (d > 0.001 and flatDir.Unit * math.max(d - reach, 0) or Vector3.zero)
        if inAnyHazard(Vector3.new(stand.X, h.Position.Y, stand.Z), 0) then
            S.waitAt = S.waitAt or os.clock()
            if os.clock() - S.waitAt < K.WAIT_MAX then
                S.goal = nil
                return
            end
        else
            S.waitAt = nil
        end

        S.aim = tpos

        -- SWING TOLERANCE > ARRIVAL TOLERANCE, OR THEY DEADLOCK.
        if d > reach + K.SWING_SLACK then
            -- SMOOTH THE THING WE ARE CHASING: a hand's bone jitters.
            local smooth = tpos
            if kind == "hand" then
                local prev = S.trackPos
                if prev and (prev - tpos).Magnitude < K.TRACK_JUMP then
                    smooth = prev:Lerp(tpos, 1 - math.exp(-K.TICK / K.TRACK_TAU))
                end
                S.trackPos = smooth
            else
                S.trackPos = nil
            end
            S.goal = { pos = smooth, reach = reach }
            return
        end

        -- IN RANGE. Keep circling if the hole is on us.
        local orbit = orbitPoint(tpos, reach)
        if orbit then
            S.goal = { pos = orbit, reach = 0 }
            every("orbit", 3, "black hole is on us - orbiting the target")
        else
            S.goal = nil
        end
        if inHaz then return end

        -- IN RANGE: STAND STILL, THE CAMERA IS WATCHING. Turn only when the
        -- aim is genuinely off, and ease into it.
        local flat = Vector3.new(tpos.X - h.Position.X, 0, tpos.Z - h.Position.Z)
        if flat.Magnitude > 0.1 then
            local wantDir = flat.Unit
            local haveDir = h.CFrame.LookVector * Vector3.new(1, 0, 1)
            haveDir = haveDir.Magnitude > 0.001 and haveDir.Unit or wantDir
            if haveDir:Dot(wantDir) < K.AIM_COS then
                local cur = h.CFrame
                h.CFrame = cur:Lerp(CFrame.lookAt(cur.Position, cur.Position + wantDir), K.AIM_EASE)
            end
        end
        -- The swing is the only part that needs a bat - and one that is off
        -- cooldown, or the game swallows it.
        if not bat or not bat.Parent then return end
        if bat:GetAttribute("CooldownActive") == true then return end
        if os.clock() - S.lastSwingAt < K.SWING_GAP then return end
        S.lastSwingAt = os.clock()
        batSwing(bat)
        stats.swings = stats.swings + 1
        if stats.swings % 20 == 1 then
            log.info("swinging at the %s (%d swings)", tostring(kind), stats.swings)
        end
    end

    ---------- THE VOID WATCH, arena half ----------

    local function voidTick()
        if not S.inArena then return end
        local c, h = ch.get(), ch.root()
        if not (c and h) then return end
        local pos = h.Position
        local gy = groundAt(pos)
        if gy and math.abs(pos.Y - gy) <= K.MAX_RISE then
            S.voidAnchor = Vector3.new(pos.X, gy, pos.Z)
            S.voidMisses = 0
            return
        end
        -- ONE MISSED RAYCAST IS NOT A FALL. It needs a run of misses AND real
        -- evidence: well below the last ground we stood on.
        if not gy then S.voidMisses = S.voidMisses + 1 else S.voidMisses = 0 end
        local falling = S.voidAnchor and (pos.Y < S.voidAnchor.Y - K.VOID_DROP_PROOF)
        if S.voidMisses >= K.VOID_MISSES and falling then
            S.voidMisses = 0
            local back = S.voidAnchor or arenaCentre()
            if back then
                stats.voidSaves = stats.voidSaves + 1
                h.AssemblyLinearVelocity = Vector3.zero
                h.AssemblyAngularVelocity = Vector3.zero
                c:MoveTo(back)
                h.CFrame = CFrame.new(back)
                log.info("voidwatch: off the floor at (%.0f, %.0f, %.0f) - pulled back (#%d)",
                    pos.X, pos.Y, pos.Z, stats.voidSaves)
                task.wait(0.3)
            end
        end
    end

    ---------- the status line ----------

    -- Held data only; the Event tab's painter calls this once a second.
    function M.status()
        if not enabled then return { title = "Auto fight", body = "off" } end
        if auto.isRunning() then
            return { title = "Auto fight", body = "ON  \u{B7}  waiting for Auto Steal to finish" }
        end
        if not S.inArena then
            local held = boss.held()
            if held and held.Open == true then
                return { title = "Auto fight", body = boss.autoEnterOn()
                    and "ON  \u{B7}  boss open - entering"
                    or "ON  \u{B7}  boss open - press Enter or turn on Auto enter" }
            end
            return { title = "Auto fight", body = "ON  \u{B7}  waiting for the boss world to open" }
        end
        if S.left then return { title = "Auto fight", body = "Boss dead  \u{B7}  leaving" } end
        local ph = S.phase
        if not ph then return { title = "Auto fight", body = "In the arena  \u{B7}  boss spawning" } end
        local what = S.kind and ("hitting the " .. S.kind) or "holding"
        return { title = "Auto fight",
                 body = ("Fighting  \u{B7}  %s  \u{B7}  %s  \u{B7}  %d swings"):format(ph, what, stats.swings) }
    end

    ---------- on / off ----------

    function M.setEnabled(on)
        on = on and true or false
        if on == enabled then return true end

        if not on then
            enabled = false
            if sc then sc:destroy() sc = nil end
            if S then
                S.goal, S.dodge, S.aim = nil, nil, nil
                setNoclip(false)
            end
            S = nil
            log.info("off (%d swings, %d kills this session)", stats.swings, stats.kills)
            return true
        end

        -- Reading the arena needs the boss watcher for the health snapshot.
        if not boss.isOn() then boss.setEnabled(true) end

        S = fresh()
        sc = BX.scope("features.bossfight")
        enabled = true

        sc:onFrame("mover", svc.RunService.Heartbeat, moverStep)
        sc:loop("fight", K.TICK, fightTick)
        sc:loop("void", K.VOID_GAP, voidTick)
        if K.DODGE then
            sc:loop("dodge", K.DODGE_GAP, function()
                if S.inArena then dodgeHazards() end
            end)
        end
        -- A respawn is a fresh character: forget the old one's floor, goal
        -- and bat, and let the tick re-detect the arena.
        ch.onSpawn(sc, "bossfight.respawn", function()
            if not S then return end
            -- Collision back first: the tick re-applies it if we are still
            -- inside, and a respawn on the main map must not stay noclipped.
            setNoclip(false)
            S.goal, S.dodge, S.aim, S.trackPos, S.batFor = nil, nil, nil, nil, nil
            S.lastSolid, S.arenaFloorY, S.left = nil, nil, false
            S.voidAnchor, S.voidMisses = nil, 0
            S.inArena, S.noclipped = false, false
            -- Hold the mover even if InBossArena never flips false across the
            -- death (so the fightTick entry edge does not re-fire): the fresh
            -- root still needs to settle before we write to it.
            S.settleUntil = os.clock() + K.RESPAWN_SETTLE
        end)

        log.info("on (tick %.2fs, swing %.2fs, dodge %s) - waiting for the arena",
            K.TICK, K.SWING_GAP, K.DODGE and "on" or "off")
        return true
    end

    return M
end)

-- ==========================================================================
-- OREO x BLYXO 4.1.0 INTEGRATION
-- Auto Steal engine below is preserved from the supplied BlyxoHub 4.1.0
-- generated source. Only this adapter binds it to OREO's UI.
-- ==========================================================================

local OreoAuto = BX.require("features.autosteal")
local OreoEggs = BX.require("features.eggs")
local OreoDevice = BX.require("core.device")
local OreoTreadmill = BX.require("features.treadmill")
local OreoEggESP = BX.require("features.esp.eggs")
local OreoPlotESP = BX.require("features.esp.plot")
local OreoBoss = BX.require("features.boss")
local OreoBossFight = BX.require("features.bossfight")
local OreoLog = BX.require("boot.log").for_module("oreo.integration")

-- The original Main tab runs Anti Treadmill ON by default. Keep that behavior.
BX.try("oreo.treadmill.default", function()
    OreoTreadmill.setEnabled(true)
end)

local function oreoNotify(titleText, bodyText, good)
    if not ScreenGui or not ScreenGui.Parent then return end
    local box = Instance.new("Frame")
    box.Size = UDim2.new(0, 360, 0, 58)
    box.Position = UDim2.new(0.5, -180, 0, 24)
    box.BackgroundColor3 = C.Card
    box.BorderSizePixel = 0
    box.ZIndex = 220
    box.Parent = ScreenGui
    Instance.new("UICorner", box).CornerRadius = UDim.new(0, 12)
    local stroke = Instance.new("UIStroke")
    stroke.Color = good == false and C.Danger or C.Accent
    stroke.Thickness = 1.5
    stroke.Transparency = 0.15
    stroke.Parent = box

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, -20, 0, 22)
    title.Position = UDim2.new(0, 10, 0, 6)
    title.BackgroundTransparency = 1
    title.Text = tostring(titleText or "OREO HUB")
    title.TextColor3 = C.Text
    title.TextSize = 13
    title.Font = Enum.Font.GothamBold
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.ZIndex = 221
    title.Parent = box

    local body = Instance.new("TextLabel")
    body.Size = UDim2.new(1, -20, 0, 22)
    body.Position = UDim2.new(0, 10, 0, 29)
    body.BackgroundTransparency = 1
    body.Text = tostring(bodyText or "")
    body.TextColor3 = C.TextDim
    body.TextSize = 11
    body.Font = Enum.Font.Gotham
    body.TextXAlignment = Enum.TextXAlignment.Left
    body.TextTruncate = Enum.TextTruncate.AtEnd
    body.ZIndex = 221
    body.Parent = box

    task.delay(3.5, function()
        if not box or not box.Parent then return end
        TweenService:Create(box, TweenInfo.new(0.25), {BackgroundTransparency = 1}):Play()
        TweenService:Create(title, TweenInfo.new(0.25), {TextTransparency = 1}):Play()
        TweenService:Create(body, TweenInfo.new(0.25), {TextTransparency = 1}):Play()
        task.wait(0.28)
        if box then box:Destroy() end
    end)
end

-- Managed toggle: same OREO look, but exposes Set() so Auto Steal can switch
-- itself OFF after a confirmed delivery, matching Blyxo Main's behavior.
local function createManagedToggle(parent, text, default, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -10, 0, 40)
    frame.BackgroundColor3 = C.Card
    frame.BorderSizePixel = 0
    frame.ZIndex = 8
    frame.Parent = parent
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)
    local stroke = Instance.new("UIStroke")
    stroke.Color = C.Border; stroke.Thickness = 1; stroke.Transparency = 0.5; stroke.Parent = frame

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -80, 1, 0); lbl.Position = UDim2.new(0, 14, 0, 0)
    lbl.BackgroundTransparency = 1; lbl.Text = text; lbl.TextColor3 = C.Text
    lbl.TextSize = 13; lbl.Font = Enum.Font.GothamMedium
    lbl.TextXAlignment = Enum.TextXAlignment.Left; lbl.ZIndex = 9; lbl.Parent = frame

    local track = Instance.new("Frame")
    track.Size = UDim2.new(0, 44, 0, 22); track.Position = UDim2.new(1, -58, 0.5, -11)
    track.BorderSizePixel = 0; track.ZIndex = 9; track.Parent = frame
    Instance.new("UICorner", track).CornerRadius = UDim.new(1, 0)

    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0, 16, 0, 16)
    knob.BackgroundColor3 = Color3.fromRGB(255,255,255)
    knob.BorderSizePixel = 0; knob.ZIndex = 10; knob.Parent = track
    Instance.new("UICorner", knob).CornerRadius = UDim.new(1, 0)

    local state = default and true or false
    local internalWrite = false
    local function paint(animated)
        local props1 = {BackgroundColor3 = state and C.Accent or C.CardHover}
        local props2 = {Position = state and UDim2.new(1,-19,0.5,-8) or UDim2.new(0,3,0.5,-8)}
        if animated then
            TweenService:Create(track, TweenInfo.new(0.25, Enum.EasingStyle.Quint), props1):Play()
            TweenService:Create(knob, TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.Out), props2):Play()
        else
            track.BackgroundColor3 = props1.BackgroundColor3
            knob.Position = props2.Position
        end
    end
    paint(false)

    local handle = {}
    function handle:Get() return state end
    function handle:Set(v, silent)
        v = v and true or false
        if state == v then return end
        state = v
        paint(true)
        if not silent and callback then
            task.spawn(function() pcall(callback, state, true) end)
        end
    end

    local click = Instance.new("TextButton")
    click.Size = UDim2.new(1,0,1,0); click.BackgroundTransparency = 1
    click.Text = ""; click.ZIndex = 11; click.Parent = frame
    click.MouseButton1Click:Connect(function()
        state = not state
        paint(true)
        if callback then pcall(callback, state, false) end
    end)
    handle.Frame = frame
    return handle
end

-- OREO-style dropdown with Refresh/Set methods. The identity is always UID;
-- labels are display only, exactly as Blyxo's Main implementation requires.
local function createDropdown(parent, text, initialOptions, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -10, 0, 42)
    frame.BackgroundColor3 = C.Card
    frame.BorderSizePixel = 0
    frame.ZIndex = 20
    frame.ClipsDescendants = true
    frame.Parent = parent
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)
    local stroke = Instance.new("UIStroke")
    stroke.Color = C.Border; stroke.Thickness = 1; stroke.Transparency = 0.5; stroke.Parent = frame

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0, 105, 0, 42)
    label.Position = UDim2.new(0, 14, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = text
    label.TextColor3 = C.TextDim
    label.TextSize = 12
    label.Font = Enum.Font.GothamMedium
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.ZIndex = 22
    label.Parent = frame

    local selected = Instance.new("TextButton")
    selected.Size = UDim2.new(1, -132, 0, 30)
    selected.Position = UDim2.new(0, 118, 0, 6)
    selected.BackgroundColor3 = C.CardHover
    selected.BorderSizePixel = 0
    selected.Text = "Select egg..."
    selected.TextColor3 = C.Text
    selected.TextSize = 11
    selected.Font = Enum.Font.Gotham
    selected.TextXAlignment = Enum.TextXAlignment.Left
    selected.TextTruncate = Enum.TextTruncate.AtEnd
    selected.ZIndex = 22
    selected.Parent = frame
    Instance.new("UICorner", selected).CornerRadius = UDim.new(0, 8)
    local p = Instance.new("UIPadding"); p.PaddingLeft = UDim.new(0,10); p.PaddingRight = UDim.new(0,10); p.Parent = selected

    local holder = Instance.new("Frame")
    holder.Size = UDim2.new(1, -28, 0, 0)
    holder.Position = UDim2.new(0, 14, 0, 42)
    holder.BackgroundColor3 = C.BgLight
    holder.BorderSizePixel = 0
    holder.ClipsDescendants = true
    holder.ZIndex = 24
    holder.Parent = frame
    Instance.new("UICorner", holder).CornerRadius = UDim.new(0, 8)

    local scroll = Instance.new("ScrollingFrame")
    scroll.Size = UDim2.new(1, 0, 1, 0)
    scroll.BackgroundTransparency = 1
    scroll.BorderSizePixel = 0
    scroll.ScrollBarThickness = 3
    scroll.ScrollBarImageColor3 = C.Accent
    scroll.CanvasSize = UDim2.new(0,0,0,0)
    scroll.ZIndex = 25
    scroll.Parent = holder
    local listLayout = Instance.new("UIListLayout")
    listLayout.Padding = UDim.new(0, 3)
    listLayout.Parent = scroll

    local open = false
    local options = {}
    local current = nil
    local buttons = {}

    local function setOpen(v)
        open = v and true or false
        local h = open and math.min(210, math.max(36, #options * 31 + 6)) or 0
        TweenService:Create(frame, TweenInfo.new(0.2, Enum.EasingStyle.Quint), {Size = UDim2.new(1,-10,0,42+h)}):Play()
        TweenService:Create(holder, TweenInfo.new(0.2, Enum.EasingStyle.Quint), {Size = UDim2.new(1,-28,0,h)}):Play()
    end

    local function rebuild()
        for _, b in ipairs(buttons) do if b then b:Destroy() end end
        buttons = {}
        for _, option in ipairs(options) do
            local b = Instance.new("TextButton")
            b.Size = UDim2.new(1, -6, 0, 28)
            b.BackgroundColor3 = C.Card
            b.BackgroundTransparency = 0.12
            b.BorderSizePixel = 0
            b.Text = tostring(option)
            b.TextColor3 = C.Text
            b.TextSize = 11
            b.Font = Enum.Font.Gotham
            b.TextXAlignment = Enum.TextXAlignment.Left
            b.TextTruncate = Enum.TextTruncate.AtEnd
            b.ZIndex = 26
            b.Parent = scroll
            Instance.new("UICorner", b).CornerRadius = UDim.new(0, 7)
            local pad = Instance.new("UIPadding"); pad.PaddingLeft = UDim.new(0,10); pad.PaddingRight = UDim.new(0,8); pad.Parent = b
            b.MouseButton1Click:Connect(function()
                current = option
                selected.Text = tostring(option)
                setOpen(false)
                if callback then pcall(callback, option) end
            end)
            buttons[#buttons+1] = b
        end
        scroll.CanvasSize = UDim2.new(0,0,0,#options * 31 + 6)
    end

    local handle = {}
    function handle:Refresh(newOptions)
        options = type(newOptions) == "table" and newOptions or {"No eggs found"}
        rebuild()
        if open then setOpen(true) end
    end
    function handle:Set(value, fire)
        current = value
        selected.Text = value and tostring(value) or "Select egg..."
        if fire and callback then pcall(callback, value) end
    end
    function handle:Get() return current end

    selected.MouseButton1Click:Connect(function() setOpen(not open) end)
    handle:Refresh(initialOptions or {"No eggs found"})
    return handle
end

createSection(mainPage, "STEAL EGG (BLYXO 4.1.0)")

local stealInfo = Instance.new("TextLabel")
stealInfo.Size = UDim2.new(1, -10, 0, 46)
stealInfo.BackgroundColor3 = C.BgLight
stealInfo.BorderSizePixel = 0
stealInfo.Text = "Pick an egg and enable Auto Steal. It baits the Forest guard, steals the selected egg, returns it to your safe zone, then stops."
stealInfo.TextWrapped = true
stealInfo.TextColor3 = C.TextDim
stealInfo.TextSize = 11
stealInfo.Font = Enum.Font.Gotham
stealInfo.TextXAlignment = Enum.TextXAlignment.Left
stealInfo.ZIndex = 8
stealInfo.Parent = mainPage
Instance.new("UICorner", stealInfo).CornerRadius = UDim.new(0, 8)
local siPad = Instance.new("UIPadding")
siPad.PaddingLeft = UDim.new(0, 12); siPad.PaddingRight = UDim.new(0, 12); siPad.Parent = stealInfo

local MAX_EGGS = 40
local labelToUid = {}
local rows = {}
local selectedUid = nil
local dropdown

local function labelFor(egg)
    local suffix = ""
    if egg.guardHeld then suffix = "  (guard)"
    elseif egg.dropped then suffix = "  (floor)" end
    return ("%s  |  %s/s%s"):format(egg.name, OreoEggs.formatRate(egg.value), suffix)
end

local function labelName(value)
    if type(value) ~= "string" then return nil end
    return value:match("^(.-)%s%s|%s%s") or value
end

local function buildEggOptions()
    local list = OreoEggs.list({}, true)
    labelToUid = {}
    rows = {}
    local out, used = {}, {}
    for i, egg in ipairs(list) do
        if i > MAX_EGGS then break end
        local label = labelFor(egg)
        if used[label] then
            local n = used[label] + 1
            used[label] = n
            label = label .. ("  #%d"):format(n)
        else
            used[label] = 1
        end
        labelToUid[label] = egg.uid
        rows[#rows+1] = egg
        out[#out+1] = label
    end
    if #out == 0 then out[1] = "No eggs found" end
    return out
end

local function eggForLabel(value)
    if type(value) ~= "string" or value == "" or value == "No eggs found" then return nil end
    local uid = labelToUid[value]
    if uid then
        for _, e in ipairs(rows) do
            if e.uid == uid then return e end
        end
        return {uid = uid, name = labelName(value) or value}
    end
    local want = labelName(value)
    if want then
        for _, e in ipairs(rows) do
            if e.name == want then return e end
        end
    end
    return nil
end

local refreshing = false
local function refreshEggDropdown(reason)
    if refreshing or not dropdown then return end
    refreshing = true
    local options = BX.offthread(buildEggOptions, 5)
    BX.try("oreo.main.refresh", function()
        if type(options) ~= "table" then
            OreoLog.warn("refresh: egg read timed out - list unchanged")
            oreoNotify("Auto Steal", "Egg refresh timed out.", false)
            return
        end
        local keep = nil
        if selectedUid then
            for label, uid in pairs(labelToUid) do
                if uid == selectedUid then keep = label break end
            end
            if not keep then
                selectedUid = nil
                OreoAuto.setOptions("main", {uid = nil})
            end
        end
        dropdown:Refresh(options)
        if keep then dropdown:Set(keep, false) end
    end)
    refreshing = false
    OreoLog.trace("refresh (%s): %d rows", tostring(reason), #rows)
end

dropdown = createDropdown(mainPage, "Target Egg", BX.offthread(buildEggOptions, 5) or {"No eggs found"}, function(value)
    local egg = eggForLabel(value)
    selectedUid = egg and egg.uid or nil
    OreoAuto.setOptions("main", {uid = selectedUid})
    OreoLog.info("target: %s (uid=%s)", tostring(value), tostring(selectedUid))
end)

createButton(mainPage, "Refresh Egg List", function()
    refreshEggDropdown("button")
end)

local autoToggle
local selfWrites = 0
local _persist = false

autoToggle = createManagedToggle(mainPage, "Auto Steal", false, function(on, programmatic)
    if on then
        selfWrites = 0
        _persist = true
        OreoAuto.setEnabled(true, "main")
        return
    end
    if selfWrites > 0 then
        selfWrites = selfWrites - 1
        return
    end
    _persist = false
    OreoAuto.setEnabled(false, "main")
end)

OreoAuto.onStop(function(why, whose)
    if whose and whose ~= "main" then return end
    if not _persist then return end
    task.spawn(function()
        OreoAuto.setEnabled(false, "main")
        task.wait()
        OreoAuto.setEnabled(true, "main")
    end)
end)


-- ======================================================================
-- BOSS EVENT (Blyxo 4.1.0 original boss + bossfight modules)
-- UI adapter only; fight/enter/mastery logic above is untouched.
-- ======================================================================
local bossPage = createTab("boss", "Boss Event", 4)

local function createStatusCard(parent, titleText, bodyText)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -10, 0, 58)
    frame.BackgroundColor3 = C.Card
    frame.BorderSizePixel = 0
    frame.ZIndex = 8
    frame.Parent = parent
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)
    local stroke = Instance.new("UIStroke")
    stroke.Color = C.Border; stroke.Thickness = 1; stroke.Transparency = 0.5; stroke.Parent = frame

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, -24, 0, 22)
    title.Position = UDim2.new(0, 12, 0, 6)
    title.BackgroundTransparency = 1
    title.Text = tostring(titleText or "Status")
    title.TextColor3 = C.AccentGlow
    title.TextSize = 12
    title.Font = Enum.Font.GothamBold
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.ZIndex = 9
    title.Parent = frame

    local body = Instance.new("TextLabel")
    body.Size = UDim2.new(1, -24, 0, 22)
    body.Position = UDim2.new(0, 12, 0, 29)
    body.BackgroundTransparency = 1
    body.Text = tostring(bodyText or "")
    body.TextColor3 = C.TextDim
    body.TextSize = 11
    body.Font = Enum.Font.Gotham
    body.TextXAlignment = Enum.TextXAlignment.Left
    body.TextTruncate = Enum.TextTruncate.AtEnd
    body.ZIndex = 9
    body.Parent = frame

    local h = {}
    function h:Set(st)
        if type(st) ~= "table" then return end
        if st.title ~= nil then title.Text = tostring(st.title) end
        if st.body ~= nil then body.Text = tostring(st.body) end
    end
    h.Frame, h.Title, h.Body = frame, title, body
    return h
end

createSection(bossPage, "ABYSS OVERLORD")
local bossStatus = createStatusCard(bossPage, "Abyss Overlord", "Reading...")

createButton(bossPage, "Refresh Boss Status", function()
    if not OreoBoss.isOn() then OreoBoss.setEnabled(true) end
    OreoBoss.refresh()
end)

createButton(bossPage, "Enter the Boss World", function()
    if not OreoBoss.isOn() then OreoBoss.setEnabled(true) end
    task.spawn(function()
        local ok, why = OreoBoss.enter()
        oreoNotify("Boss Event", tostring(why), ok)
    end)
end)

local bossAutoEnterToggle = createManagedToggle(bossPage, "Auto Enter", false, function(on)
    if on and not OreoBoss.isOn() then OreoBoss.setEnabled(true) end
    OreoBoss.setAutoEnter(on)
    oreoNotify("Boss Event", "Auto enter " .. (on and "ON" or "OFF"), true)
end)

createSection(bossPage, "AUTO FIGHT")
local fightStatus = createStatusCard(bossPage, "Auto fight", "off")
local bossFightToggle = createManagedToggle(bossPage, "Auto Fight", false, function(on)
    local ok, why = OreoBossFight.setEnabled(on)
    if ok == false then
        oreoNotify("Boss Event", tostring(why or "Auto fight refused"), false)
    else
        oreoNotify("Boss Event", "Auto fight " .. (on and "ON" or "OFF"), true)
    end
end)

createButton(bossPage, "Claim Mastery Rewards", function()
    task.spawn(function()
        local n, msg = OreoBoss.claimMilestones()
        oreoNotify("Boss Mastery", tostring(msg), (tonumber(n) or 0) > 0)
    end)
end)

-- Same behavior as Blyxo's Event tab: the boss watcher is read-only and is
-- armed when the tab exists. Auto enter/fight remain explicit user toggles.
BX.try("oreo.boss.defaultWatcher", function()
    OreoBoss.setEnabled(true)
end)

createSection(mainPage, "ESP")
local eggEspToggle = createManagedToggle(mainPage, "Egg ESP", false, function(on)
    BX.try("oreo.eggEsp", function() OreoEggESP.setEnabled(on) end)
end)
local plotEspToggle = createManagedToggle(mainPage, "Plot ESP", false, function(on)
    BX.try("oreo.plotEsp", function() OreoPlotESP.setEnabled(on) end)
end)

createSection(mainPage, "AUTO STEAL SUPPORT")
local treadmillToggle = createManagedToggle(mainPage, "Anti Treadmill", true, function(on)
    BX.try("oreo.treadmill", function() OreoTreadmill.setEnabled(on) end)
end)

-- Same slow correctness check as Blyxo Main: if selected UID vanishes, rebuild.
local oreoUiScope = BX.scope("oreo.autosteal.ui")
oreoUiScope:loop("selected-prune", OreoDevice.scale(10), function()
    if not selectedUid then return end
    local still = OreoEggs.get(selectedUid)
    if not still then refreshEggDropdown("selected egg vanished") end
end)
oreoUiScope:loop("boss-painter", 1, function()
    if bossStatus then bossStatus:Set(OreoBoss.status()) end
    if fightStatus then fightStatus:Set(OreoBossFight.status()) end
end)


-- Cleanup when OREO is really unloaded (not merely hidden with X/minimize).
ScreenGui.Destroying:Connect(function()
    pcall(function() OreoAuto.setEnabled(false, "main") end)
    pcall(function() OreoEggESP.setEnabled(false) end)
    pcall(function() OreoPlotESP.setEnabled(false) end)
    pcall(function() OreoTreadmill.setEnabled(false) end)
    pcall(function() OreoBossFight.setEnabled(false) end)
    pcall(function() OreoBoss.setEnabled(false) end)
    pcall(function() oreoUiScope:destroy() end)
    pcall(BX.teardown)
end)

OreoLog.info("OREO integration ready: Blyxo Auto Steal 4.1.0 + ESP + Boss Event")
print("OREO HUB: Blyxo Auto Steal 4.1.0 + Boss Event integration ready")
end)
