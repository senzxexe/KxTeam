local CoreGui = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")

local LocalPlayer = Players.LocalPlayer

if CoreGui:FindFirstChild("KarpiwareCustomUI") then
    CoreGui.KarpiwareCustomUI:Destroy()
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "KXCustomUI"
ScreenGui.Parent = CoreGui
ScreenGui.ResetOnSpawn = false

-- Color Palette (100% Ultimate Pure Black)
local Theme = {
    Background = Color3.fromRGB(0, 0, 0),
    Sidebar = Color3.fromRGB(0, 0, 0),
    Accent = Color3.fromRGB(168, 85, 247),
    NeonBright = Color3.fromRGB(220, 140, 255),
    TextPrimary = Color3.fromRGB(245, 245, 245),
    TextSecondary = Color3.fromRGB(140, 140, 140),
    Border = Color3.fromRGB(20, 20, 20),
    ToggleOff = Color3.fromRGB(10, 10, 10),
    ToggleKnob = Color3.fromRGB(230, 230, 230)
}

-- Floating Toggle Button (Tombol Mengambang Buka/Tutup Menu)
local FloatingBtn = Instance.new("ImageButton")
FloatingBtn.Name = "FloatingBtn"
FloatingBtn.Parent = ScreenGui
FloatingBtn.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
FloatingBtn.BorderSizePixel = 0
FloatingBtn.Position = UDim2.new(0, 30, 0.5, -25)
FloatingBtn.Size = UDim2.new(0, 48, 0, 48)
FloatingBtn.AutoButtonColor = false
FloatingBtn.Image = "rbxassetid://87903194695906"
FloatingBtn.Active = true
FloatingBtn.Draggable = true
FloatingBtn.ZIndex = 100

local FloatCorner = Instance.new("UICorner")
FloatCorner.CornerRadius = UDim.new(1, 0)
FloatCorner.Parent = FloatingBtn

local FloatStroke = Instance.new("UIStroke")
FloatStroke.Color = Theme.Accent
FloatStroke.Thickness = 1.5
FloatStroke.Parent = FloatingBtn

-- Main Window (Size: 420 x 320)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Theme.Background
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.5, -210, 0.5, -160)
MainFrame.Size = UDim2.new(0, 420, 0, 320)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = true
MainFrame.ClipsDescendants = true

local isMenuOpen = true
FloatingBtn.MouseButton1Click:Connect(function()
    isMenuOpen = not isMenuOpen
    MainFrame.Visible = isMenuOpen
    
    local targetColor = isMenuOpen and Theme.Accent or Color3.fromRGB(40, 40, 40)
    TweenService:Create(FloatStroke, TweenInfo.new(0.2), {Color = targetColor}):Play()
end)

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Name = "MainStroke"
MainStroke.Color = Theme.Border
MainStroke.Thickness = 1.2
MainStroke.Parent = MainFrame

-- Running Outline Particle Effect Frame
local RunningOutline = Instance.new("Frame")
RunningOutline.Name = "RunningOutline"
RunningOutline.Parent = MainFrame
RunningOutline.BackgroundColor3 = Theme.NeonBright
RunningOutline.BackgroundTransparency = 0.1
RunningOutline.BorderSizePixel = 0
RunningOutline.Size = UDim2.new(0, 70, 0, 2)
RunningOutline.ZIndex = 20
RunningOutline.Visible = false

local OutlineCorner = Instance.new("UICorner")
OutlineCorner.CornerRadius = UDim.new(1, 0)
OutlineCorner.Parent = RunningOutline

local isOutlineActive = false
local outlineConnection = nil

local function StartRunningOutlineAnimation()
    if outlineConnection then outlineConnection:Disconnect() end
    local tickTime = 0
    outlineConnection = RunService.RenderStepped:Connect(function(dt)
        if not isOutlineActive or not MainFrame.Visible then return end
        tickTime = tickTime + (dt * 4.5)
        
        local perimeter = 1480
        local distance = (tickTime * 520) % perimeter
        
        local w, h = 420, 320
        local x, y = 0, 0
        
        if distance < w then
            x = distance
            y = 0
        elseif distance < w + h then
            x = w
            y = distance - w
        elseif distance < (w * 2) + h then
            x = w - (distance - (w + h))
            y = h
        else
            x = 0
            y = h - (distance - ((w * 2) + h))
        end
        
        RunningOutline.Position = UDim2.new(0, x - 35, 0, y - 1)
        
        local glowPulse = math.abs(math.sin(tickTime * 6))
        RunningOutline.BackgroundColor3 = Color3.fromRGB(
            math.clamp(168 + (glowPulse * 87), 0, 255),
            math.clamp(85 + (glowPulse * 125), 0, 255),
            255
        )
    end)
end

StartRunningOutlineAnimation()

-- Sidebar (Left)
local Sidebar = Instance.new("Frame")
Sidebar.Name = "Sidebar"
Sidebar.Parent = MainFrame
Sidebar.BackgroundColor3 = Theme.Sidebar
Sidebar.BorderSizePixel = 0
Sidebar.Size = UDim2.new(0, 115, 1, 0)
Sidebar.ZIndex = 6

local SidebarCorner = Instance.new("UICorner")
SidebarCorner.CornerRadius = UDim.new(0, 10)
SidebarCorner.Parent = Sidebar

local SidebarFix = Instance.new("Frame")
SidebarFix.Parent = Sidebar
SidebarFix.BackgroundColor3 = Theme.Sidebar
SidebarFix.BorderSizePixel = 0
SidebarFix.Position = UDim2.new(1, -5, 0, 0)
SidebarFix.Size = UDim2.new(0, 5, 1, 0)

-- Logo / Title
local LogoTitle = Instance.new("TextLabel")
LogoTitle.Parent = Sidebar
LogoTitle.BackgroundTransparency = 1
LogoTitle.Position = UDim2.new(0, 10, 0, 12)
LogoTitle.Size = UDim2.new(0, 95, 0, 20)
LogoTitle.Font = Enum.Font.GothamBold
LogoTitle.Text = "KXTeam"
LogoTitle.TextColor3 = Theme.Accent
LogoTitle.TextSize = 13
LogoTitle.TextXAlignment = Enum.TextXAlignment.Left
LogoTitle.ZIndex = 7

local LogoSub = Instance.new("TextLabel")
LogoSub.Parent = Sidebar
LogoSub.BackgroundTransparency = 1
LogoSub.Position = UDim2.new(0, 10, 0, 30)
LogoSub.Size = UDim2.new(0, 95, 0, 15)
LogoSub.Font = Enum.Font.GothamMedium
LogoSub.Text = "v6.1.0 • Universal"
LogoSub.TextColor3 = Theme.TextSecondary
LogoSub.TextSize = 8
LogoSub.TextXAlignment = Enum.TextXAlignment.Left
LogoSub.ZIndex = 7

-- PLAYER CARD / HEADER DI POJOK KIRI BAWAH SIDEBAR
local PlayerCard = Instance.new("Frame")
PlayerCard.Name = "PlayerCard"
PlayerCard.Parent = Sidebar
PlayerCard.BackgroundColor3 = Color3.fromRGB(8, 8, 8)
PlayerCard.BorderSizePixel = 0
PlayerCard.Position = UDim2.new(0, 8, 1, -42)
PlayerCard.Size = UDim2.new(1, -16, 0, 34)
PlayerCard.ZIndex = 8

local CardCorner = Instance.new("UICorner")
CardCorner.CornerRadius = UDim.new(0, 6)
CardCorner.Parent = PlayerCard

local CardStroke = Instance.new("UIStroke")
CardStroke.Color = Color3.fromRGB(25, 25, 25)
CardStroke.Thickness = 1
CardStroke.Parent = PlayerCard

-- Avatar Thumbnail Player
local AvatarImg = Instance.new("ImageLabel")
AvatarImg.Name = "AvatarImg"
AvatarImg.Parent = PlayerCard
AvatarImg.BackgroundTransparency = 1
AvatarImg.Position = UDim2.new(0, 4, 0.5, -11)
AvatarImg.Size = UDim2.new(0, 22, 0, 22)
AvatarImg.ZIndex = 9

local AvatarCorner = Instance.new("UICorner")
AvatarCorner.CornerRadius = UDim.new(1, 0)
AvatarCorner.Parent = AvatarImg

-- Mengambil thumbnail kepala player otomatis
pcall(function()
    local thumbType = Enum.ThumbnailType.HeadShot
    local thumbSize = Enum.ThumbnailSize.Size420x420
    local content, isReady = Players:GetUserThumbnailAsync(LocalPlayer.UserId, thumbType, thumbSize)
    AvatarImg.Image = content
end)

-- Nama Player
local PlayerName = Instance.new("TextLabel")
PlayerName.Name = "PlayerName"
PlayerName.Parent = PlayerCard
PlayerName.BackgroundTransparency = 1
PlayerName.Position = UDim2.new(0, 30, 0, 4)
PlayerName.Size = UDim2.new(1, -32, 0, 13)
PlayerName.Font = Enum.Font.GothamBold
PlayerName.Text = LocalPlayer and LocalPlayer.Name or "ranzmodz"
PlayerName.TextColor3 = Theme.TextPrimary
PlayerName.TextSize = 9
PlayerName.TextXAlignment = Enum.TextXAlignment.Left
PlayerName.ZIndex = 9

-- Status / Role Player
local PlayerStatus = Instance.new("TextLabel")
PlayerStatus.Name = "PlayerStatus"
PlayerStatus.Parent = PlayerCard
PlayerStatus.BackgroundTransparency = 1
PlayerStatus.Position = UDim2.new(0, 30, 0, 17)
PlayerStatus.Size = UDim2.new(1, -32, 0, 13)
PlayerStatus.Font = Enum.Font.GothamMedium
PlayerStatus.Text = "Premium User"
PlayerStatus.TextColor3 = Theme.Accent
PlayerStatus.TextSize = 8
PlayerStatus.TextXAlignment = Enum.TextXAlignment.Left
PlayerStatus.ZIndex = 9

-- Tab Button Container (Disesuaikan ukurannya agar tidak menumpuk dengan Player Card)
local TabContainer = Instance.new("ScrollingFrame")
TabContainer.Parent = Sidebar
TabContainer.Active = true
TabContainer.BackgroundTransparency = 1
TabContainer.BorderSizePixel = 0
TabContainer.Position = UDim2.new(0, 0, 0, 55)
TabContainer.Size = UDim2.new(1, 0, 1, -105)
TabContainer.CanvasSize = UDim2.new(0, 0, 0, 200)
TabContainer.ScrollBarThickness = 1
TabContainer.ScrollBarImageColor3 = Theme.Accent
TabContainer.ZIndex = 7

local TabListLayout = Instance.new("UIListLayout")
TabListLayout.Parent = TabContainer
TabListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
TabListLayout.SortOrder = Enum.SortOrder.LayoutOrder
TabListLayout.Padding = UDim.new(0, 4)

-- Content Container (Right)
local ContentContainer = Instance.new("Frame")
ContentContainer.Name = "ContentContainer"
ContentContainer.Parent = MainFrame
ContentContainer.BackgroundTransparency = 1
ContentContainer.Position = UDim2.new(0, 124, 0, 10)
ContentContainer.Size = UDim2.new(1, -134, 1, -20)
ContentContainer.ZIndex = 6

local Tabs = {}
local TabPages = {}
local ActiveSectionContainer = nil

local function CreateTabContent(name)
    local TabPage = Instance.new("Frame")
    TabPage.Name = name .. "Page"
    TabPage.Parent = ContentContainer
    TabPage.BackgroundTransparency = 1
    TabPage.Size = UDim2.new(1, 0, 1, 0)
    TabPage.Visible = false
    TabPage.ZIndex = 6

    local LeftColumn = Instance.new("ScrollingFrame")
    LeftColumn.Name = "LeftColumn"
    LeftColumn.Parent = TabPage
    LeftColumn.Active = true
    LeftColumn.BackgroundTransparency = 1
    LeftColumn.BorderSizePixel = 0
    LeftColumn.Position = UDim2.new(0, 4, 0, 0)
    LeftColumn.Size = UDim2.new(0.5, -8, 1, 0)
    LeftColumn.CanvasSize = UDim2.new(0, 0, 2, 0)
    LeftColumn.ScrollBarThickness = 2
    LeftColumn.ScrollBarImageColor3 = Theme.Accent
    LeftColumn.ZIndex = 6

    local LeftLayout = Instance.new("UIListLayout")
    LeftLayout.Parent = LeftColumn
    LeftLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    LeftLayout.SortOrder = Enum.SortOrder.LayoutOrder
    LeftLayout.Padding = UDim.new(0, 8)

    local RightColumn = Instance.new("ScrollingFrame")
    RightColumn.Name = "RightColumn"
    RightColumn.Parent = TabPage
    RightColumn.Active = true
    RightColumn.BackgroundTransparency = 1
    RightColumn.BorderSizePixel = 0
    RightColumn.Position = UDim2.new(0.5, 4, 0, 0)
    RightColumn.Size = UDim2.new(0.5, -8, 1, 0)
    RightColumn.CanvasSize = UDim2.new(0, 0, 2, 0)
    RightColumn.ScrollBarThickness = 2
    RightColumn.ScrollBarImageColor3 = Theme.Accent
    RightColumn.ZIndex = 6

    local RightLayout = Instance.new("UIListLayout")
    RightLayout.Parent = RightColumn
    RightLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    RightLayout.SortOrder = Enum.SortOrder.LayoutOrder
    RightLayout.Padding = UDim.new(0, 8)

    return TabPage, LeftColumn, RightColumn
end

local function AddCategory(tabName, sectionTitle, side)
    local parentCol = (side:lower() == "right") and TabPages[tabName].Right or TabPages[tabName].Left

    local HeaderLabel = Instance.new("TextLabel")
    HeaderLabel.Parent = parentCol
    HeaderLabel.BackgroundTransparency = 1
    HeaderLabel.Size = UDim2.new(1, -6, 0, 20)
    HeaderLabel.Position = UDim2.new(0, 6, 0, 0)
    HeaderLabel.Font = Enum.Font.GothamBold
    HeaderLabel.Text = sectionTitle:upper()
    HeaderLabel.TextColor3 = Theme.Accent
    HeaderLabel.TextSize = 10
    HeaderLabel.TextXAlignment = Enum.TextXAlignment.Left
    HeaderLabel.ZIndex = 8

    local SectionContainer = Instance.new("Frame")
    SectionContainer.Name = "SectionContainer"
    SectionContainer.Parent = parentCol
    SectionContainer.BackgroundTransparency = 1
    SectionContainer.Size = UDim2.new(1, -6, 0, 0)
    SectionContainer.ZIndex = 8

    local SectionLayout = Instance.new("UIListLayout")
    SectionLayout.Parent = SectionContainer
    SectionLayout.SortOrder = Enum.SortOrder.LayoutOrder
    SectionLayout.Padding = UDim.new(0, 6)

    SectionLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        SectionContainer.Size = UDim2.new(1, -6, 0, SectionLayout.AbsoluteContentSize.Y)
    end)

    ActiveSectionContainer = SectionContainer
    return SectionContainer
end

local function AddToggle(text, callback)
    if not ActiveSectionContainer then return end
    
    local ToggleBtn = Instance.new("TextButton")
    ToggleBtn.Name = "ToggleBtn"
    ToggleBtn.Parent = ActiveSectionContainer
    ToggleBtn.BackgroundTransparency = 1
    ToggleBtn.BorderSizePixel = 0
    ToggleBtn.Size = UDim2.new(1, -12, 0, 22)
    ToggleBtn.Position = UDim2.new(0, 8, 0, 0)
    ToggleBtn.AutoButtonColor = false
    ToggleBtn.Font = Enum.Font.GothamMedium
    ToggleBtn.Text = text
    ToggleBtn.TextColor3 = Theme.TextSecondary
    ToggleBtn.TextSize = 10
    ToggleBtn.TextXAlignment = Enum.TextXAlignment.Left
    ToggleBtn.ZIndex = 9

    local SwitchBg = Instance.new("Frame")
    SwitchBg.Name = "SwitchBg"
    SwitchBg.Parent = ToggleBtn
    SwitchBg.BackgroundColor3 = Theme.ToggleOff
    SwitchBg.BorderSizePixel = 0
    SwitchBg.Position = UDim2.new(1, -26, 0.5, -7)
    SwitchBg.Size = UDim2.new(0, 30, 0, 14)
    SwitchBg.ZIndex = 9

    local SwitchCorner = Instance.new("UICorner")
    SwitchCorner.CornerRadius = UDim.new(1, 0)
    SwitchCorner.Parent = SwitchBg

    local SwitchStroke = Instance.new("UIStroke")
    SwitchStroke.Color = Color3.fromRGB(20, 20, 20)
    SwitchStroke.Thickness = 1
    SwitchStroke.Parent = SwitchBg

    local Knob = Instance.new("Frame")
    Knob.Name = "Knob"
    Knob.Parent = SwitchBg
    Knob.BackgroundColor3 = Theme.ToggleKnob
    Knob.BorderSizePixel = 0
    Knob.Position = UDim2.new(0, 2, 0.5, -5)
    Knob.Size = UDim2.new(0, 10, 0, 10)
    Knob.ZIndex = 10

    local KnobCorner = Instance.new("UICorner")
    KnobCorner.CornerRadius = UDim.new(1, 0)
    KnobCorner.Parent = Knob

    local state = false
    local function SetState(newState)
        state = newState
        local goalColor = state and Theme.Accent or Theme.ToggleOff
        local goalKnobPos = state and UDim2.new(1, -12, 0.5, -5) or UDim2.new(0, 2, 0.5, -5)
        local goalTextColor = state and Theme.TextPrimary or Theme.TextSecondary

        TweenService:Create(SwitchBg, TweenInfo.new(0.2), {BackgroundColor3 = goalColor}):Play()
        TweenService:Create(Knob, TweenInfo.new(0.2), {Position = goalKnobPos}):Play()
        ToggleBtn.TextColor3 = goalTextColor

        if callback then callback(state) end
    end

    ToggleBtn.MouseButton1Click:Connect(function()
        SetState(not state)
    end)
end

local function AddSlider(text, min, max, default, callback)
    if not ActiveSectionContainer then return end
    
    local SliderFrame = Instance.new("Frame")
    SliderFrame.Name = "SliderFrame"
    SliderFrame.Parent = ActiveSectionContainer
    SliderFrame.BackgroundTransparency = 1
    SliderFrame.BorderSizePixel = 0
    SliderFrame.Size = UDim2.new(1, -12, 0, 35)
    SliderFrame.Position = UDim2.new(0, 8, 0, 0)
    SliderFrame.ZIndex = 9

    local TitleLabel = Instance.new("TextLabel")
    TitleLabel.Parent = SliderFrame
    TitleLabel.BackgroundTransparency = 1
    TitleLabel.Position = UDim2.new(0, 0, 0, 0)
    TitleLabel.Size = UDim2.new(1, 0, 0, 15)
    TitleLabel.Font = Enum.Font.GothamMedium
    TitleLabel.Text = text
    TitleLabel.TextColor3 = Theme.TextSecondary
    TitleLabel.TextSize = 10
    TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
    TitleLabel.ZIndex = 10

    local ValueLabel = Instance.new("TextLabel")
    ValueLabel.Parent = SliderFrame
    ValueLabel.BackgroundTransparency = 1
    ValueLabel.Position = UDim2.new(0, 0, 0, 0)
    ValueLabel.Size = UDim2.new(1, 0, 0, 15)
    ValueLabel.Font = Enum.Font.GothamBold
    ValueLabel.Text = tostring(default)
    ValueLabel.TextColor3 = Theme.TextPrimary
    ValueLabel.TextSize = 10
    ValueLabel.TextXAlignment = Enum.TextXAlignment.Right
    ValueLabel.ZIndex = 10

    local SliderBar = Instance.new("Frame")
    SliderBar.Name = "SliderBar"
    SliderBar.Parent = SliderFrame
    SliderBar.BackgroundColor3 = Theme.ToggleOff
    SliderBar.BorderSizePixel = 0
    SliderBar.Position = UDim2.new(0, 0, 0, 22)
    SliderBar.Size = UDim2.new(1, 0, 0, 6)
    SliderBar.ZIndex = 10

    local BarCorner = Instance.new("UICorner")
    BarCorner.CornerRadius = UDim.new(1, 0)
    BarCorner.Parent = SliderBar

    local SliderFill = Instance.new("Frame")
    SliderFill.Name = "SliderFill"
    SliderFill.Parent = SliderBar
    SliderFill.BackgroundColor3 = Theme.Accent
    SliderFill.BorderSizePixel = 0
    SliderFill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
    SliderFill.ZIndex = 11

    local FillCorner = Instance.new("UICorner")
    FillCorner.CornerRadius = UDim.new(1, 0)
    FillCorner.Parent = SliderFill

    local value = default
    local dragging = false

    local function UpdateSlider(input)
        local posx = math.clamp((input.Position.X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X, 0, 1)
        SliderFill.Size = UDim2.new(posx, 0, 1, 0)
        value = math.floor(min + ((max - min) * posx))
        ValueLabel.Text = tostring(value)
        if callback then callback(value) end
    end

    SliderBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            UpdateSlider(input)
        end
    end)

    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            UpdateSlider(input)
        end
    end)
end

local TabNames = {"AIMBOT", "VISUAL", "MISC", "BRUTAL", "INFO"}

for i, name in ipairs(TabNames) do
    local TabBtn = Instance.new("TextButton")
    TabBtn.Parent = TabContainer
    TabBtn.BackgroundTransparency = 1
    TabBtn.BorderSizePixel = 0
    TabBtn.Size = UDim2.new(1, -12, 0, 28)
    TabBtn.Font = Enum.Font.GothamMedium
    TabBtn.Text = "  " .. name
    TabBtn.TextColor3 = Theme.TextSecondary
    TabBtn.TextSize = 10
    TabBtn.TextXAlignment = Enum.TextXAlignment.Left
    TabBtn.ZIndex = 7

    local TabCorner = Instance.new("UICorner")
    TabCorner.CornerRadius = UDim.new(0, 6)
    TabCorner.Parent = TabBtn

    local Page, LeftCol, RightCol = CreateTabContent(name)
    Tabs[name] = Page
    TabPages[name] = {Left = LeftCol, Right = RightCol}

    TabBtn.MouseButton1Click:Connect(function()
        for _, p in pairs(Tabs) do
            p.Visible = false
        end
        for _, b in pairs(TabContainer:GetChildren()) do
            if b:IsA("TextButton") then
                b.TextColor3 = Theme.TextSecondary
                b.BackgroundTransparency = 1
            end
        end
        Page.Visible = true
        TabBtn.TextColor3 = Theme.TextPrimary
        TabBtn.BackgroundTransparency = 0
        TabBtn.BackgroundColor3 = Color3.fromRGB(12, 12, 12)
    end)

    if i == 1 then
        Page.Visible = true
        TabBtn.TextColor3 = Theme.TextPrimary
        TabBtn.BackgroundTransparency = 0
        TabBtn.BackgroundColor3 = Color3.fromRGB(12, 12, 12)
    end
end

-- ================================================================= --
-- PEMANGGILAN FITUR & 
