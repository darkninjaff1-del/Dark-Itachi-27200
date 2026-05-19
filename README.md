--[[
    🔴 BLOX FRUITS - PREMIUM PROTOTYPE UI
    Dark/Anime Theme | Itachi Uchiha Inspired
    Educational Purpose Only
    Version: 1.0.0 Prototype
]]

-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer

-- Variables
local ScriptName = "ItachiHub"
local ScriptVersion = "v1.0.0"

-- Configuration System
local Config = {
    Farm = {
        AutoFarm = false,
        AutoQuest = false,
        FastAttack = false,
        AutoFarmLevel = false,
        BringMobs = false,
        AutoBoss = false
    },
    Combat = {
        AutoClick = false,
        CPS = 20,
        FastClick = false,
        AutoHaki = false,
        AutoEquipWeapon = false
    },
    PvP = {
        AimFOV = 200,
        ShowFOV = false,
        Aimbot = false,
        AimAssist = false,
        AllyCheck = false,
        TeamCheck = false,
        VisibilityCheck = false,
        Whitelist = {}
    },
    Player = {
        WalkSpeed = 16,
        JumpPower = 50,
        InfiniteJump = false
    },
    Misc = {
        AntiAFK = false,
        RejoinServer = false
    }
}

-- Save/Load Config
local function SaveConfig()
    local success, err = pcall(function()
        if writefile and isfolder and makefolder then
            if not isfolder("ItachiHub") then
                makefolder("ItachiHub")
            end
            local http = game:GetService("HttpService")
            local json = http:JSONEncode(Config)
            writefile("ItachiHub/config.json", json)
        end
    end)
    return success
end

local function LoadConfig()
    local success, err = pcall(function()
        if readfile and isfile then
            if isfile("ItachiHub/config.json") then
                local http = game:GetService("HttpService")
                local json = readfile("ItachiHub/config.json")
                local loadedConfig = http:JSONDecode(json)
                for category, settings in pairs(loadedConfig) do
                    if Config[category] then
                        for key, value in pairs(settings) do
                            if Config[category][key] ~= nil then
                                Config[category][key] = value
                            end
                        end
                    end
                end
            end
        end
    end)
    return success
end

-- UI Library (Custom Built)
local ItachiHub = {
    Flags = {},
    Connections = {},
    Theme = {
        Primary = Color3.fromRGB(255, 0, 0),
        Secondary = Color3.fromRGB(180, 0, 0),
        Accent = Color3.fromRGB(255, 50, 50),
        Background = Color3.fromRGB(10, 10, 10),
        Surface = Color3.fromRGB(20, 20, 20),
        SurfaceLight = Color3.fromRGB(30, 30, 30),
        Text = Color3.fromRGB(255, 255, 255),
        TextDim = Color3.fromRGB(150, 150, 150),
        Glow = Color3.fromRGB(255, 0, 0),
        Success = Color3.fromRGB(0, 255, 100),
    }
}

-- Create Main GUI
local function CreateGUI()
    -- ScreenGui
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "ItachiHub"
    ScreenGui.Parent = game.CoreGui
    ScreenGui.ResetOnSpawn = false
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

    -- ============================================
    -- ÍCONE FLUTUANTE (TOGGLE BUTTON)
    -- ============================================
    local ToggleIcon = Instance.new("TextButton")
    ToggleIcon.Name = "ToggleIcon"
    ToggleIcon.Size = UDim2.new(0, 50, 0, 50)
    ToggleIcon.Position = UDim2.new(0, 20, 0.5, -25) -- Lateral esquerda no meio
    ToggleIcon.BackgroundColor3 = ItachiHub.Theme.Primary
    ToggleIcon.BackgroundTransparency = 0.3
    ToggleIcon.Text = "🔴"
    ToggleIcon.TextSize = 28
    ToggleIcon.Font = Enum.Font.GothamBold
    ToggleIcon.BorderSizePixel = 0
    ToggleIcon.ZIndex = 10
    ToggleIcon.Parent = ScreenGui

    -- Arredondar o ícone
    local ToggleCorner = Instance.new("UICorner")
    ToggleCorner.CornerRadius = UDim.new(1, 0) -- Círculo
    ToggleCorner.Parent = ToggleIcon

    -- Brilho no ícone
    local ToggleStroke = Instance.new("UIStroke")
    ToggleStroke.Color = Color3.fromRGB(255, 255, 255)
    ToggleStroke.Thickness = 2
    ToggleStroke.Transparency = 0.5
    ToggleStroke.Parent = ToggleIcon

    -- Tooltip simples (Label abaixo do ícone)
    local ToggleLabel = Instance.new("TextLabel")
    ToggleLabel.Size = UDim2.new(0, 100, 0, 20)
    ToggleLabel.Position = UDim2.new(0, -25, 1, 5) -- Centralizado abaixo do ícone
    ToggleLabel.BackgroundTransparency = 1
    ToggleLabel.Text = "ITACHI HUB"
    ToggleLabel.TextColor3 = ItachiHub.Theme.Text
    ToggleLabel.TextSize = 12
    ToggleLabel.Font = Enum.Font.GothamBold
    ToggleLabel.TextStrokeTransparency = 0.5
    ToggleLabel.Parent = ToggleIcon

    -- Animação de pulsar no ícone
    local pulseAnim = TweenService:Create(ToggleIcon, TweenInfo.new(1.5, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {
        BackgroundTransparency = 0.6
    })
    pulseAnim:Play()

    -- ============================================
    -- MAIN UI
    -- ============================================
    -- Main Container (Draggable)
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 600, 0, 400)
    MainFrame.Position = UDim2.new(0.5, -300, 0.5, -200)
    MainFrame.BackgroundColor3 = ItachiHub.Theme.Background
    MainFrame.BackgroundTransparency = 0.1
    MainFrame.BorderSizePixel = 0
    MainFrame.ClipsDescendants = true
    MainFrame.Visible = false -- COMEÇA ESCONDIDO
    MainFrame.Parent = ScreenGui

    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 12)
    UICorner.Parent = MainFrame

    local UIStroke = Instance.new("UIStroke")
    UIStroke.Color = ItachiHub.Theme.Primary
    UIStroke.Thickness = 2
    UIStroke.Transparency = 0.5
    UIStroke.Parent = MainFrame

    -- Função para Mostrar/Esconder UI
    local uiVisible = false
    local function ToggleUI()
        uiVisible = not uiVisible
        MainFrame.Visible = uiVisible
        
        if uiVisible then
            -- Animação de entrada
            MainFrame.Size = UDim2.new(0, 0, 0, 0)
            MainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
            local openTween = TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Back), {
                Size = UDim2.new(0, 600, 0, 400),
                Position = UDim2.new(0.5, -300, 0.5, -200)
            })
            openTween:Play()
            
            -- Muda ícone para "fechado"
            ToggleIcon.Text = "✕"
        else
            -- Muda ícone para "aberto"
            ToggleIcon.Text = "🔴"
        end
    end

    -- Conectar clique no ícone
    ToggleIcon.MouseButton1Click:Connect(ToggleUI)

    -- Também permitir arrastar o ícone (opcional)
    local iconDragging
    local iconDragStart
    local iconStartPos
    ToggleIcon.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton2 then -- Botão direito arrasta
            iconDragging = true
            iconDragStart = input.Position
            iconStartPos = ToggleIcon.Position
        end
    end)
    ToggleIcon.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton2 then
            iconDragging = false
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if iconDragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - iconDragStart
            ToggleIcon.Position = UDim2.new(
                iconStartPos.X.Scale, iconStartPos.X.Offset + delta.X,
                iconStartPos.Y.Scale, iconStartPos.Y.Offset + delta.Y
            )
        end
    end)

    -- Title Bar
    local TitleBar = Instance.new("Frame")
    TitleBar.Name = "TitleBar"
    TitleBar.Size = UDim2.new(1, 0, 0, 40)
    TitleBar.BackgroundColor3 = ItachiHub.Theme.Surface
    TitleBar.BackgroundTransparency = 0.3
    TitleBar.BorderSizePixel = 0
    TitleBar.Parent = MainFrame

    local TitleBarCorner = Instance.new("UICorner")
    TitleBarCorner.CornerRadius = UDim.new(0, 12)
    TitleBarCorner.Parent = TitleBar

    local TitleLabel = Instance.new("TextLabel")
    TitleLabel.Size = UDim2.new(0, 200, 1, 0)
    TitleLabel.Position = UDim2.new(0, 20, 0, 0)
    TitleLabel.BackgroundTransparency = 1
    TitleLabel.Text = "🔴 ITACHI HUB"
    TitleLabel.TextColor3 = ItachiHub.Theme.Primary
    TitleLabel.TextSize = 22
    TitleLabel.Font = Enum.Font.GothamBold
    TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
    TitleLabel.Parent = TitleBar

    local VersionLabel = Instance.new("TextLabel")
    VersionLabel.Size = UDim2.new(0, 80, 0, 20)
    VersionLabel.Position = UDim2.new(1, -100, 0.5, -10)
    VersionLabel.BackgroundTransparency = 1
    VersionLabel.Text = ScriptVersion
    VersionLabel.TextColor3 = ItachiHub.Theme.TextDim
    VersionLabel.TextSize = 14
    VersionLabel.Font = Enum.Font.Gotham
    VersionLabel.Parent = TitleBar

    -- Minimize Button
    local MinimizeButton = Instance.new("TextButton")
    MinimizeButton.Size = UDim2.new(0, 30, 0, 30)
    MinimizeButton.Position = UDim2.new(1, -70, 0, 5)
    MinimizeButton.BackgroundColor3 = ItachiHub.Theme.Accent
    MinimizeButton.BackgroundTransparency = 0.5
    MinimizeButton.Text = "🗕"
    MinimizeButton.TextColor3 = ItachiHub.Theme.Text
    MinimizeButton.TextSize = 18
    MinimizeButton.Font = Enum.Font.GothamBold
    MinimizeButton.BorderSizePixel = 0
    local MinCorner = Instance.new("UICorner")
    MinCorner.CornerRadius = UDim.new(0, 6)
    MinCorner.Parent = MinimizeButton
    MinimizeButton.Parent = TitleBar
    MinimizeButton.MouseButton1Click:Connect(ToggleUI) -- Minimizar também usa toggle

    -- Tab System
    local TabContainer = Instance.new("Frame")
    TabContainer.Name = "TabContainer"
    TabContainer.Size = UDim2.new(0, 140, 1, -50)
    TabContainer.Position = UDim2.new(0, 0, 0, 50)
    TabContainer.BackgroundColor3 = ItachiHub.Theme.Surface
    TabContainer.BackgroundTransparency = 0.5
    TabContainer.BorderSizePixel = 0
    TabContainer.Parent = MainFrame

    -- Content Area
    local ContentFrame = Instance.new("Frame")
    ContentFrame.Name = "ContentFrame"
    ContentFrame.Size = UDim2.new(1, -150, 1, -60)
    ContentFrame.Position = UDim2.new(0, 145, 0, 55)
    ContentFrame.BackgroundTransparency = 1
    ContentFrame.ClipsDescendants = true
    ContentFrame.Parent = MainFrame

    -- Create Tabs
    local Tabs = {"Farm", "Combat", "PvP", "Player", "Misc"}
    local TabButtons = {}
    local TabContents = {}

    for i, tabName in ipairs(Tabs) do
        local TabButton = Instance.new("TextButton")
        TabButton.Name = tabName
        TabButton.Size = UDim2.new(1, -20, 0, 35)
        TabButton.Position = UDim2.new(0, 10, 0, 10 + (i-1) * 45)
        TabButton.BackgroundColor3 = ItachiHub.Theme.SurfaceLight
        TabButton.BackgroundTransparency = 0.7
        TabButton.Text = tabName
        TabButton.TextColor3 = ItachiHub.Theme.TextDim
        TabButton.TextSize = 16
        TabButton.Font = Enum.Font.GothamBold
        TabButton.BorderSizePixel = 0

        local TabCorner = Instance.new("UICorner")
        TabCorner.CornerRadius = UDim.new(0, 8)
        TabCorner.Parent = TabButton

        TabButton.Parent = TabContainer
        TabButtons[tabName] = TabButton

        local TabContent = Instance.new("ScrollingFrame")
        TabContent.Name = tabName .. "Content"
        TabContent.Size = UDim2.new(1, -10, 1, -10)
        TabContent.Position = UDim2.new(0, 5, 0, 5)
        TabContent.BackgroundTransparency = 1
        TabContent.BorderSizePixel = 0
        TabContent.ScrollBarThickness = 4
        TabContent.ScrollBarImageColor3 = ItachiHub.Theme.Primary
        TabContent.Visible = (i == 1)
        TabContent.CanvasSize = UDim2.new(0, 0, 0, 0)
        TabContent.Parent = ContentFrame

        TabContents[tabName] = TabContent

        TabButton.MouseButton1Click:Connect(function()
            for name, btn in pairs(TabButtons) do
                btn.BackgroundColor3 = ItachiHub.Theme.SurfaceLight
                btn.BackgroundTransparency = 0.7
                btn.TextColor3 = ItachiHub.Theme.TextDim
            end
            TabButton.BackgroundColor3 = ItachiHub.Theme.Primary
            TabButton.BackgroundTransparency = 0.3
            TabButton.TextColor3 = ItachiHub.Theme.Text

            for name, content in pairs(TabContents) do
                content.Visible = false
            end
            TabContent.Visible = true
        end)
    end

    if TabButtons["Farm"] then
        TabButtons["Farm"].BackgroundColor3 = ItachiHub.Theme.Primary
        TabButtons["Farm"].BackgroundTransparency = 0.3
        TabButtons["Farm"].TextColor3 = ItachiHub.Theme.Text
    end

    -- Toggle Creation Function
    local function CreateToggle(category, name, parent, yPos)
        local ToggleFrame = Instance.new("Frame")
        ToggleFrame.Size = UDim2.new(1, -10, 0, 40)
        ToggleFrame.Position = UDim2.new(0, 5, 0, yPos)
        ToggleFrame.BackgroundColor3 = ItachiHub.Theme.Surface
        ToggleFrame.BackgroundTransparency = 0.5
        ToggleFrame.BorderSizePixel = 0

        local ToggleCorner = Instance.new("UICorner")
        ToggleCorner.CornerRadius = UDim.new(0, 8)
        ToggleCorner.Parent = ToggleFrame

        local ToggleLabel = Instance.new("TextLabel")
        ToggleLabel.Size = UDim2.new(0.7, 0, 1, 0)
        ToggleLabel.Position = UDim2.new(0, 10, 0, 0)
        ToggleLabel.BackgroundTransparency = 1
        ToggleLabel.Text = name
        ToggleLabel.TextColor3 = ItachiHub.Theme.Text
        ToggleLabel.TextSize = 14
        ToggleLabel.Font = Enum.Font.Gotham
        ToggleLabel.TextXAlignment = Enum.TextXAlignment.Left
        ToggleLabel.Parent = ToggleFrame

        local ToggleButton = Instance.new("TextButton")
        ToggleButton.Size = UDim2.new(0, 44, 0, 24)
        ToggleButton.Position = UDim2.new(1, -55, 0.5, -12)
        ToggleButton.BackgroundColor3 = ItachiHub.Theme.Secondary
        ToggleButton.Text = ""
        ToggleButton.BorderSizePixel = 0
        ToggleButton.AutoButtonColor = false

        local ToggleBtnCorner = Instance.new("UICorner")
        ToggleBtnCorner.CornerRadius = UDim.new(1, 0)
        ToggleBtnCorner.Parent = ToggleButton

        local ToggleIndicator = Instance.new("Frame")
        ToggleIndicator.Size = UDim2.new(0, 20, 0, 20)
        ToggleIndicator.Position = UDim2.new(0, 2, 0.5, -10)
        ToggleIndicator.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        ToggleIndicator.BorderSizePixel = 0

        local IndicatorCorner = Instance.new("UICorner")
        IndicatorCorner.CornerRadius = UDim.new(1, 0)
        IndicatorCorner.Parent = ToggleIndicator

        ToggleIndicator.Parent = ToggleButton
        ToggleButton.Parent = ToggleFrame

        local isEnabled = Config[category] and Config[category][name] or false

        if isEnabled then
            ToggleButton.BackgroundColor3 = ItachiHub.Theme.Primary
            ToggleIndicator.Position = UDim2.new(1, -22, 0.5, -10)
        else
            ToggleButton.BackgroundColor3 = ItachiHub.Theme.SurfaceLight
            ToggleIndicator.Position = UDim2.new(0, 2, 0.5, -10)
        end

        local connection
        connection = ToggleButton.MouseButton1Click:Connect(function()
            isEnabled = not isEnabled
            if Config[category] then
                Config[category][name] = isEnabled
            end

            if isEnabled then
                TweenService:Create(ToggleButton, TweenInfo.new(0.2), {
                    BackgroundColor3 = ItachiHub.Theme.Primary
                }):Play()
                TweenService:Create(ToggleIndicator, TweenInfo.new(0.2), {
                    Position = UDim2.new(1, -22, 0.5, -10)
                }):Play()
            else
                TweenService:Create(ToggleButton, TweenInfo.new(0.2), {
                    BackgroundColor3 = ItachiHub.Theme.SurfaceLight
                }):Play()
                TweenService:Create(ToggleIndicator, TweenInfo.new(0.2), {
                    Position = UDim2.new(0, 2, 0.5, -10)
                }):Play()
            end
        end)

        table.insert(ItachiHub.Connections, connection)
        ToggleFrame.Parent = parent
        return ToggleFrame
    end

    -- Slider Creation Function
    local function CreateSlider(category, name, min, max, default, parent, yPos)
        local SliderFrame = Instance.new("Frame")
        SliderFrame.Size = UDim2.new(1, -10, 0, 60)
        SliderFrame.Position = UDim2.new(0, 5, 0, yPos)
        SliderFrame.BackgroundColor3 = ItachiHub.Theme.Surface
        SliderFrame.BackgroundTransparency = 0.5
        SliderFrame.BorderSizePixel = 0

        local SliderCorner = Instance.new("UICorner")
        SliderCorner.CornerRadius = UDim.new(0, 8)
        SliderCorner.Parent = SliderFrame

        local SliderLabel = Instance.new("TextLabel")
        SliderLabel.Size = UDim2.new(1, -20, 0, 20)
        SliderLabel.Position = UDim2.new(0, 10, 0, 5)
        SliderLabel.BackgroundTransparency = 1
        SliderLabel.Text = name .. ": " .. default
        SliderLabel.TextColor3 = ItachiHub.Theme.Text
        SliderLabel.TextSize = 14
        SliderLabel.Font = Enum.Font.Gotham
        SliderLabel.TextXAlignment = Enum.TextXAlignment.Left
        SliderLabel.Parent = SliderFrame

        local SliderButton = Instance.new("TextButton")
        SliderButton.Size = UDim2.new(0, 20, 0, 20)
        SliderButton.Position = UDim2.new(0, 10, 0, 32)
        SliderButton.BackgroundColor3 = ItachiHub.Theme.Primary
        SliderButton.Text = ""
        SliderButton.BorderSizePixel = 0
        SliderButton.AutoButtonColor = false

        local SliderBtnCorner = Instance.new("UICorner")
        SliderBtnCorner.CornerRadius = UDim.new(1, 0)
        SliderBtnCorner.Parent = SliderButton

        local SliderBar = Instance.new("Frame")
        SliderBar.Size = UDim2.new(1, -40, 0, 4)
        SliderBar.Position = UDim2.new(0, 20, 0, 40)
        SliderBar.BackgroundColor3 = ItachiHub.Theme.SurfaceLight
        SliderBar.BorderSizePixel = 0
        SliderBar.Parent = SliderFrame

        local SliderFill = Instance.new("Frame")
        SliderFill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
        SliderFill.BackgroundColor3 = ItachiHub.Theme.Primary
        SliderFill.BorderSizePixel = 0
        SliderFill.Parent = SliderBar

        SliderButton.Parent = SliderBar
        SliderFrame.Parent = parent

        local value = default
        local dragConnection

        local function updateSlider(input)
            if SliderBar.AbsoluteSize.X <= 0 then return end
            local barSize = SliderBar.AbsoluteSize.X
            local mousePos = input.Position.X
            local barPos = SliderBar.AbsolutePosition.X
            local percent = math.clamp((mousePos - barPos) / barSize, 0, 1)
            value = min + (max - min) * percent
            value = math.round(value)

            if Config[category] then
                Config[category][name] = value
            end

            SliderFill.Size = UDim2.new(percent, 0, 1, 0)
            SliderButton.Position = UDim2.new(percent, -10, 0, 8)
            SliderLabel.Text = name .. ": " .. value
        end

        SliderButton.MouseButton1Down:Connect(function()
            dragConnection = RunService.RenderStepped:Connect(function()
                updateSlider(UserInputService:GetMouseLocation())
            end)
        end)

        UserInputService.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                if dragConnection then
                    dragConnection:Disconnect()
                    dragConnection = nil
                end
            end
        end)

        return SliderFrame
    end

    -- Populate Farm Tab
    local farmY = 0
    CreateToggle("Farm", "AutoFarm", TabContents["Farm"], farmY); farmY = farmY + 45
    CreateToggle("Farm", "AutoQuest", TabContents["Farm"], farmY); farmY = farmY + 45
    CreateToggle("Farm", "FastAttack", TabContents["Farm"], farmY); farmY = farmY + 45
    CreateToggle("Farm", "AutoFarmLevel", TabContents["Farm"], farmY); farmY = farmY + 45
    CreateToggle("Farm", "BringMobs", TabContents["Farm"], farmY); farmY = farmY + 45
    CreateToggle("Farm", "AutoBoss", TabContents["Farm"], farmY)
    TabContents["Farm"].CanvasSize = UDim2.new(0, 0, 0, farmY + 50)

    -- Populate Combat Tab
    local combatY = 0
    CreateToggle("Combat", "AutoClick", TabContents["Combat"], combatY); combatY = combatY + 45
    CreateSlider("Combat", "CPS", 1, 30, Config.Combat.CPS, TabContents["Combat"], combatY); combatY = combatY + 65
    CreateToggle("Combat", "FastClick", TabContents["Combat"], combatY); combatY = combatY + 45
    CreateToggle("Combat", "AutoHaki", TabContents["Combat"], combatY); combatY = combatY + 45
    CreateToggle("Combat", "AutoEquipWeapon", TabContents["Combat"], combatY)
    TabContents["Combat"].CanvasSize = UDim2.new(0, 0, 0, combatY + 50)

    -- Populate PvP Tab
    local pvpY = 0
    CreateSlider("PvP", "AimFOV", 50, 500, Config.PvP.AimFOV, TabContents["PvP"], pvpY); pvpY = pvpY + 65
    CreateToggle("PvP", "ShowFOV", TabContents["PvP"], pvpY); pvpY = pvpY + 45
    CreateToggle("PvP", "Aimbot", TabContents["PvP"], pvpY); pvpY = pvpY + 45
    CreateToggle("PvP", "AimAssist", TabContents["PvP"], pvpY); pvpY = pvpY + 45

    -- Separator
    local Separator = Instance.new("Frame")
    Separator.Size = UDim2.new(1, -10, 0, 2)
    Separator.Position = UDim2.new(0, 5, 0, pvpY + 10)
    Separator.BackgroundColor3 = ItachiHub.Theme.Primary
    Separator.BackgroundTransparency = 0.5
    Separator.BorderSizePixel = 0
    Separator.Parent = TabContents["PvP"]

    local SeparatorLabel = Instance.new("TextLabel")
    SeparatorLabel.Size = UDim2.new(1, 0, 0, 20)
    SeparatorLabel.Position = UDim2.new(0, 0, 0, pvpY + 20)
    SeparatorLabel.BackgroundTransparency = 1
    SeparatorLabel.Text = "🔴 PROTECTION SYSTEM"
    SeparatorLabel.TextColor3 = ItachiHub.Theme.Primary
    SeparatorLabel.TextSize = 16
    SeparatorLabel.Font = Enum.Font.GothamBold
    SeparatorLabel.TextXAlignment = Enum.TextXAlignment.Left
    SeparatorLabel.Parent = TabContents["PvP"]

    pvpY = pvpY + 45
    CreateToggle("PvP", "AllyCheck", TabContents["PvP"], pvpY); pvpY = pvpY + 45
    CreateToggle("PvP", "TeamCheck", TabContents["PvP"], pvpY); pvpY = pvpY + 45
    CreateToggle("PvP", "VisibilityCheck", TabContents["PvP"], pvpY)
    TabContents["PvP"].CanvasSize = UDim2.new(0, 0, 0, pvpY + 50)

    -- Populate Player Tab
    local playerY = 0
    CreateSlider("Player", "WalkSpeed", 16, 200, Config.Player.WalkSpeed, TabContents["Player"], playerY); playerY = playerY + 65
    CreateSlider("Player", "JumpPower", 50, 300, Config.Player.JumpPower, TabContents["Player"], playerY); playerY = playerY + 65
    CreateToggle("Player", "InfiniteJump", TabContents["Player"], playerY)
    TabContents["Player"].CanvasSize = UDim2.new(0, 0, 0, playerY + 50)

    -- Populate Misc Tab
    local miscY = 0
    CreateToggle("Misc", "AntiAFK", TabContents["Misc"], miscY); miscY = miscY + 45

    local RejoinButton = Instance.new("TextButton")
    RejoinButton.Size = UDim2.new(1, -10, 0, 35)
    RejoinButton.Position = UDim2.new(0, 5, 0, miscY)
    RejoinButton.BackgroundColor3 = ItachiHub.Theme.Primary
    RejoinButton.BackgroundTransparency = 0.3
    RejoinButton.Text = "REJOIN SERVER"
    RejoinButton.TextColor3 = ItachiHub.Theme.Text
    RejoinButton.TextSize = 14
    RejoinButton.Font = Enum.Font.GothamBold
    RejoinButton.BorderSizePixel = 0
    local RejoinCorner = Instance.new("UICorner"); RejoinCorner.CornerRadius = UDim.new(0, 8); RejoinCorner.Parent = RejoinButton
    RejoinButton.MouseButton1Click:Connect(function()
        local ts = game:GetService("TeleportService")
        ts:Teleport(game.PlaceId)
    end)
    RejoinButton.Parent = TabContents["Misc"]
    miscY = miscY + 45

    local SaveButton = Instance.new("TextButton")
    SaveButton.Size = UDim2.new(1, -10, 0, 35)
    SaveButton.Position = UDim2.new(0, 5, 0, miscY)
    SaveButton.BackgroundColor3 = ItachiHub.Theme.Success
    SaveButton.BackgroundTransparency = 0.5
    SaveButton.Text = "💾 SAVE CONFIG"
    SaveButton.TextColor3 = ItachiHub.Theme.Text
    SaveButton.TextSize = 14
    SaveButton.Font = Enum.Font.GothamBold
    SaveButton.BorderSizePixel = 0
    local SaveCorner = Instance.new("UICorner"); SaveCorner.CornerRadius = UDim.new(0, 8); SaveCorner.Parent = SaveButton
    SaveButton.MouseButton1Click:Connect(function()
        if SaveConfig() then SaveButton.Text = "✅ CONFIG SAVED!" else SaveButton.Text = "❌ SAVE FAILED!" end
        task.delay(2, function() SaveButton.Text = "💾 SAVE CONFIG" end)
    end)
    SaveButton.Parent = TabContents["Misc"]
    miscY = miscY + 45

    local LoadButton = Instance.new("TextButton")
    LoadButton.Size = UDim2.new(1, -10, 0, 35)
    LoadButton.Position = UDim2.new(0, 5, 0, miscY)
    LoadButton.BackgroundColor3 = ItachiHub.Theme.Accent
    LoadButton.BackgroundTransparency = 0.5
    LoadButton.Text = "📂 LOAD CONFIG"
    LoadButton.TextColor3 = ItachiHub.Theme.Text
    LoadButton.TextSize = 14
    LoadButton.Font = Enum.Font.GothamBold
    LoadButton.BorderSizePixel = 0
    local LoadCorner = Instance.new("UICorner"); LoadCorner.CornerRadius = UDim.new(0, 8); LoadCorner.Parent = LoadButton
    LoadButton.MouseButton1Click:Connect(function()
        if LoadConfig() then LoadButton.Text = "✅ CONFIG LOADED!" else LoadButton.Text = "❌ NO CONFIG FOUND!" end
        task.delay(2, function() LoadButton.Text = "📂 LOAD CONFIG" end)
    end)
    LoadButton.Parent = TabContents["Misc"]
    miscY = miscY + 60
    TabContents["Misc"].CanvasSize = UDim2.new(0, 0, 0, miscY + 50)

    -- Draggable Functionality
    local dragging
    local dragInput
    local dragStart
    local startPos

    TitleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = MainFrame.Position
        end
    end)

    TitleBar.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)

    RunService.RenderStepped:Connect(function()
        if dragging and dragInput then
            local delta = dragInput.Position - dragStart
            MainFrame.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end)

    -- FOV Circle Drawing
    local FOVCircle = Instance.new("Frame")
    FOVCircle.Name = "FOVCircle"
    FOVCircle.Size = UDim2.new(0, Config.PvP.AimFOV, 0, Config.PvP.AimFOV)
    FOVCircle.Position = UDim2.new(0.5, -Config.PvP.AimFOV/2, 0.5, -Config.PvP.AimFOV/2)
    FOVCircle.BackgroundTransparency = 1
    FOVCircle.Visible = Config.PvP.ShowFOV
    FOVCircle.Parent = ScreenGui

    local FOVStroke = Instance.new("UIStroke")
    FOVStroke.Color = ItachiHub.Theme.Primary
    FOVStroke.Thickness = 2
    FOVStroke.Transparency = 0.3
    FOVStroke.Parent = FOVCircle

    local FOVCorner = Instance.new("UICorner")
    FOVCorner.CornerRadius = UDim.new(1, 0)
    FOVCorner.Parent = FOVCircle

    local fovConnection
    fovConnection = RunService.RenderStepped:Connect(function()
        FOVCircle.Visible = Config.PvP.ShowFOV
        FOVCircle.Size = UDim2.new(0, Config.PvP.AimFOV, 0, Config.PvP.AimFOV)
        FOVCircle.Position = UDim2.new(0.5, -Config.PvP.AimFOV/2, 0.5, -Config.PvP.AimFOV/2)
    end)
    table.insert(ItachiHub.Connections, fovConnection)

    return MainFrame, ScreenGui
end

-- Core Features Implementation
local function StartFeatures()
    -- Auto Click System
    local autoClickConnection = RunService.Heartbeat:Connect(function()
        if Config.Combat.AutoClick then
            task.wait(1 / (Config.Combat.CPS or 20))
        end
    end)
    table.insert(ItachiHub.Connections, autoClickConnection)

    -- Player Modifications
    local playerConnection = RunService.Heartbeat:Connect(function()
        if LocalPlayer.Character then
            local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.WalkSpeed = Config.Player.WalkSpeed
                humanoid.JumpPower = Config.Player.JumpPower
                if Config.Player.InfiniteJump then
                    humanoid.Jump = true
                end
            end
        end
    end)
    table.insert(ItachiHub.Connections, playerConnection)

    -- Anti AFK
    local antiAFKConnection = RunService.Heartbeat:Connect(function()
        if Config.Misc.AntiAFK then
            local VirtualUser = game:GetService("VirtualUser")
            VirtualUser:CaptureController()
            VirtualUser:ClickButton2(Vector2.new())
        end
    end)
    table.insert(ItachiHub.Connections, antiAFKConnection)

    -- Aimbot System
    local aimbotConnection = RunService.RenderStepped:Connect(function()
        if not Config.PvP.Aimbot then return end
        local closestPlayer, closestDistance = nil, Config.PvP.AimFOV or 200
        for _, player in ipairs(Players:GetPlayers()) do
            if player == LocalPlayer then continue end
            if Config.PvP.AllyCheck then
                local whitelisted = false
                for _, uid in ipairs(Config.PvP.Whitelist) do
                    if uid == player.UserId then whitelisted = true; break end
                end
                if whitelisted then continue end
            end
            if Config.PvP.TeamCheck and player.Team == LocalPlayer.Team then continue end
            if player.Character and player.Character:FindFirstChild("Head") then
                local head = player.Character.Head
                local screenPos, onScreen = workspace.CurrentCamera:WorldToScreenPoint(head.Position)
                if onScreen then
                    local mousePos = UserInputService:GetMouseLocation()
                    local screenCenter = Config.PvP.AimAssist and mousePos or Vector2.new(
                        workspace.CurrentCamera.ViewportSize.X / 2,
                        workspace.CurrentCamera.ViewportSize.Y / 2
                    )
                    local distance = (Vector2.new(screenPos.X, screenPos.Y) - screenCenter).Magnitude
                    if distance < closestDistance then
                        closestDistance = distance
                        closestPlayer = player
                    end
                end
            end
        end
    end)
    table.insert(ItachiHub.Connections, aimbotConnection)
end

-- Initialize UI and Features
local MainFrame, ScreenGui = CreateGUI()
StartFeatures()
LoadConfig()

-- Notification System
local function Notify(title, message, duration)
    local notification = Instance.new("Frame")
    notification.Size = UDim2.new(0, 300, 0, 80)
    notification.Position = UDim2.new(1, 320, 0, 20)
    notification.BackgroundColor3 = ItachiHub.Theme.Surface
    notification.BackgroundTransparency = 0.2
    notification.BorderSizePixel = 0
    notification.Parent = ScreenGui

    local notifCorner = Instance.new("UICorner"); notifCorner.CornerRadius = UDim.new(0, 8); notifCorner.Parent = notification
    local notifStroke = Instance.new("UIStroke"); notifStroke.Color = ItachiHub.Theme.Primary; notifStroke.Thickness = 2; notifStroke.Transparency = 0.5; notifStroke.Parent = notification

    TweenService:Create(notification, TweenInfo.new(0.5, Enum.EasingStyle.Quart), {
        Position = UDim2.new(1, -320, 0, 20)
    }):Play()

    task.delay(duration or 3, function()
        TweenService:Create(notification, TweenInfo.new(0.5, Enum.EasingStyle.Quart), {
            Position = UDim2.new(1, 320, 0, 20)
        }):Play()
        task.wait(0.5)
        notification:Destroy()
    end)
end

-- Welcome Notification
Notify("🔴 ITACHI HUB", "Prototype loaded successfully! Click 🔴 to open", 5)

-- Cleanup function
local function Cleanup()
    if ScreenGui then ScreenGui:Destroy() end
    for _, conn in ipairs(ItachiHub.Connections) do
        if conn then conn:Disconnect() end
    end
end

return {
    Config = Config,
    Cleanup = Cleanup,
    SaveConfig = SaveConfig,
    LoadConfig = LoadConfig,
    Notify = Notify
}
