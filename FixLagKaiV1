-- FixLag GUI + chức năng giảm lag
-- Tác dụng: tắt particle, giảm chất lượng render, tắt shadow, giảm water/terrain detail, xóa decals/textures
-- Sử dụng: copy vào executor và chạy

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local StarterGui = game:GetService("StarterGui")
local Workspace = game:GetService("Workspace")

local localPlayer = Players.LocalPlayer

-- Lưu settings gốc để có thể restore
local original = {
    GlobalShadows = Lighting.GlobalShadows,
    Brightness = Lighting.Brightness,
    ClockTime = Lighting.ClockTime,
    FogEnd = Lighting.FogEnd,
    Ambient = Lighting.Ambient,
    OutdoorAmbient = Lighting.OutdoorAmbient,
    terrainWaterWaveSize = nil,
    terrainWaterWaveSpeed = nil,
    qualityLevel = nil
}

pcall(function()
    if settings and settings().Rendering then
        original.qualityLevel = settings().Rendering.QualityLevel
    end
end)

-- Utils
local function safeSet(obj, prop, value)
    pcall(function() obj[prop] = value end)
end

local function removeParticleEmitters(root)
    for _, v in ipairs(root:GetDescendants()) do
        if v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Beam") or v:IsA("Smoke") or v:IsA("Fire") or v:IsA("Sparkles") then
            pcall(function() v.Enabled = false end)
        end
    end
end

local function removeDecalsAndTextures(root)
    for _, v in ipairs(root:GetDescendants()) do
        if v:IsA("Decal") or v:IsA("Texture") then
            pcall(function() v.Transparency = 1 end)
        end
    end
end

local function reduceMeshDetails(root)
    for _, v in ipairs(root:GetDescendants()) do
        if v:IsA("SpecialMesh") then
            pcall(function()
                v.LevelOfDetail = Enum.MeshLevelOfDetail.Medium
            end)
        end
    end
end

local function minimizeParts(root)
    for _, part in ipairs(root:GetDescendants()) do
        if part:IsA("BasePart") then
            pcall(function()
                part.CastShadow = false
                part.Material = Enum.Material.SmoothPlastic
            end)
        end
    end
end

-- Toggle actions
local actions = {
    lowQuality = false,
    noParticles = false,
    noTextures = false,
    noShadows = false,
    minimalParts = false
}

local function applyLowQuality(enable)
    actions.lowQuality = enable
    if enable then
        safeSet(Lighting, "GlobalShadows", false)
        safeSet(Lighting, "Brightness", 1)
        safeSet(Lighting, "ClockTime", 12)
        safeSet(Lighting, "FogEnd", 100000)
        safeSet(Lighting, "Ambient", Color3.fromRGB(200,200,200))
        safeSet(Lighting, "OutdoorAmbient", Color3.fromRGB(200,200,200))
        pcall(function()
            if settings and settings().Rendering then
                settings().Rendering.QualityLevel = 1
            end
        end)
        if Workspace:FindFirstChild("Terrain") then
            local t = Workspace.Terrain
            pcall(function()
                original.terrainWaterWaveSize = t.WaterWaveSize
                original.terrainWaterWaveSpeed = t.WaterWaveSpeed
                t.WaterWaveSize = 0
                t.WaterWaveSpeed = 0
            end)
        end
    else
        safeSet(Lighting, "GlobalShadows", original.GlobalShadows)
        safeSet(Lighting, "Brightness", original.Brightness)
        safeSet(Lighting, "ClockTime", original.ClockTime)
        safeSet(Lighting, "FogEnd", original.FogEnd)
        safeSet(Lighting, "Ambient", original.Ambient)
        safeSet(Lighting, "OutdoorAmbient", original.OutdoorAmbient)
        pcall(function()
            if settings and settings().Rendering and original.qualityLevel then
                settings().Rendering.QualityLevel = original.qualityLevel
            end
        end)
        if Workspace:FindFirstChild("Terrain") and original.terrainWaterWaveSize ~= nil then
            pcall(function()
                Workspace.Terrain.WaterWaveSize = original.terrainWaterWaveSize
                Workspace.Terrain.WaterWaveSpeed = original.terrainWaterWaveSpeed
            end)
        end
    end
end

local function applyNoParticles(enable)
    actions.noParticles = enable
    if enable then removeParticleEmitters(Workspace) end
end

local function applyNoTextures(enable)
    actions.noTextures = enable
    if enable then removeDecalsAndTextures(Workspace) end
end

local function applyNoShadows(enable)
    actions.noShadows = enable
    safeSet(Lighting, "GlobalShadows", not enable and original.GlobalShadows or false)
    for _, part in ipairs(Workspace:GetDescendants()) do
        if part:IsA("BasePart") then
            pcall(function() part.CastShadow = not enable end)
        end
    end
end

local function applyMinimalParts(enable)
    actions.minimalParts = enable
    if enable then
        minimizeParts(Workspace)
        reduceMeshDetails(Workspace)
    end
end

local function restoreAll()
    applyLowQuality(false)
    applyNoParticles(false)
    applyNoTextures(false)
    applyNoShadows(false)
    applyMinimalParts(false)
end

-- GUI
local function createGui()
    if localPlayer:FindFirstChildOfClass("PlayerGui") == nil then
        repeat task.wait() until localPlayer:FindFirstChildOfClass("PlayerGui")
    end
    local playerGui = localPlayer:FindFirstChildOfClass("PlayerGui")

    if playerGui:FindFirstChild("FixLagGui_V1") then
        playerGui.FixLagGui_V1:Destroy()
    end

    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "FixLagGui_V1"
    screenGui.ResetOnSpawn = false
    screenGui.Parent = playerGui

    local mainFrame = Instance.new("Frame")
    mainFrame.Name = "Main"
    mainFrame.Size = UDim2.new(0, 220, 0, 36)
    mainFrame.Position = UDim2.new(0, 8, 0, 8)
    mainFrame.BackgroundTransparency = 1
    mainFrame.Parent = screenGui

    local iconBtn = Instance.new("ImageButton")
    iconBtn.Name = "Icon"
    iconBtn.Parent = mainFrame
    iconBtn.Size = UDim2.new(0, 36, 0, 36)
    iconBtn.Position = UDim2.new(0, 0, 0, 0)
    iconBtn.Image = "rbxassetid://6031090990"
    iconBtn.BackgroundColor3 = Color3.fromRGB(25,25,25)
    iconBtn.BorderSizePixel = 0

    local panel = Instance.new("Frame")
    panel.Name = "Panel"
    panel.Parent = mainFrame
    panel.Size = UDim2.new(0, 220, 0, 200)
    panel.Position = UDim2.new(0, 0, 0, 40)
    panel.BackgroundColor3 = Color3.fromRGB(20,20,20)
    panel.BorderSizePixel = 0
    panel.Visible = false
    panel.ClipsDescendants = true

    -- nút X
    local closeBtn = Instance.new("TextButton")
    closeBtn.Size = UDim2.new(0, 30, 0, 30)
    closeBtn.Position = UDim2.new(1, -35, 0, 5)
    closeBtn.Text = "X"
    closeBtn.Font = Enum.Font.SourceSansBold
    closeBtn.TextSize = 18
    closeBtn.BackgroundColor3 = Color3.fromRGB(150,50,50)
    closeBtn.TextColor3 = Color3.fromRGB(255,255,255)
    closeBtn.Parent = panel
    closeBtn.MouseButton1Click:Connect(function()
        panel.Visible = false
    end)

    local uiList = Instance.new("UIListLayout")
    uiList.Padding = UDim.new(0,6)
    uiList.Parent = panel
    uiList.SortOrder = Enum.SortOrder.LayoutOrder
    uiList.HorizontalAlignment = Enum.HorizontalAlignment.Center
    uiList.VerticalAlignment = Enum.VerticalAlignment.Top

    local pad = Instance.new("UIPadding")
    pad.Parent = panel
    pad.PaddingTop = UDim.new(0,40)

    local function makeToggle(name, func)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(0, 200, 0, 32)
        btn.Text = name .. " — OFF"
        btn.Font = Enum.Font.SourceSans
        btn.TextSize = 16
        btn.Parent = panel
        btn.BackgroundColor3 = Color3.fromRGB(40,40,40)
        btn.BorderSizePixel = 0
        btn.TextColor3 = Color3.fromRGB(230,230,230)

        local state = false
        btn.MouseButton1Click:Connect(function()
            state = not state
            btn.Text = name .. (state and " — ON" or " — OFF")
            pcall(function() func(state) end)
        end)
    end

    makeToggle("Low Quality (Lighting)", applyLowQuality)
    makeToggle("Disable Particles", applyNoParticles)
    makeToggle("Hide Textures/Decals", applyNoTextures)
    makeToggle("Disable Shadows", applyNoShadows)
    makeToggle("Minimal Parts/Mesh", applyMinimalParts)

    local restoreBtn = Instance.new("TextButton")
    restoreBtn.Size = UDim2.new(0, 200, 0, 32)
    restoreBtn.Text = "Restore Defaults"
    restoreBtn.Font = Enum.Font.SourceSansBold
    restoreBtn.TextSize = 16
    restoreBtn.Parent = panel
    restoreBtn.BackgroundColor3 = Color3.fromRGB(60,60,60)
    restoreBtn.BorderSizePixel = 0
    restoreBtn.TextColor3 = Color3.fromRGB(255,255,255)
    restoreBtn.MouseButton1Click:Connect(function()
        restoreAll()
    end)

    iconBtn.MouseButton1Click:Connect(function()
        panel.Visible = not panel.Visible
    end)

    -- Kéo thả
    local dragging, dragInput, dragStart, startPos
    local function update(input)
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end

    mainFrame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = mainFrame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    mainFrame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)

    RunService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            update(input)
        end
    end)
end

createGui()

pcall(function()
    StarterGui:SetCore("ChatMakeSystemMessage", {
        Text = "[FixLag] GUI loaded ✅ Click icon để mở menu, nút X để ẩn.";
        Color = Color3.new(0.4,0.8,1);
        Font = Enum.Font.SourceSansBold;
        FontSize = Enum.FontSize.Size24;
    })
end)
