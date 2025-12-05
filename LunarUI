-- Lunar UI Library v3.0
-- GitHub: https://raw.githubusercontent.com/YOUR_USERNAME/YOUR_REPO/main/LunarUI.lua

local LunarUI = {}
LunarUI.__index = LunarUI

-- Services
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")

-- Player
local Player = Players.LocalPlayer
local Mouse = Player:GetMouse()

-- Themes
LunarUI.Themes = {
    Dark = {
        Main = Color3.fromRGB(25, 25, 35),
        Secondary = Color3.fromRGB(35, 35, 45),
        Accent = Color3.fromRGB(120, 85, 220),
        Text = Color3.fromRGB(240, 240, 245),
        Shadow = Color3.fromRGB(0, 0, 0, 0.5)
    },
    Light = {
        Main = Color3.fromRGB(245, 245, 250),
        Secondary = Color3.fromRGB(230, 230, 240),
        Accent = Color3.fromRGB(90, 130, 210),
        Text = Color3.fromRGB(30, 30, 40),
        Shadow = Color3.fromRGB(0, 0, 0, 0.2)
    },
    Purple = {
        Main = Color3.fromRGB(35, 25, 45),
        Secondary = Color3.fromRGB(50, 35, 60),
        Accent = Color3.fromRGB(190, 90, 230),
        Text = Color3.fromRGB(255, 255, 255),
        Shadow = Color3.fromRGB(0, 0, 0, 0.4)
    }
}

-- Utility Functions
local function Create(type, props)
    local obj = Instance.new(type)
    for prop, value in pairs(props) do
        if prop ~= "Parent" then
            obj[prop] = value
        end
    end
    obj.Parent = props.Parent
    return obj
end

local function Tween(obj, props, duration, easingStyle, easingDirection)
    local tweenInfo = TweenInfo.new(duration or 0.3, easingStyle or Enum.EasingStyle.Quad, easingDirection or Enum.EasingDirection.Out)
    local tween = TweenService:Create(obj, tweenInfo, props)
    tween:Play()
    return tween
end

local function RippleEffect(button)
    local ripple = Create("Frame", {
        Parent = button,
        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
        BackgroundTransparency = 0.7,
        Size = UDim2.new(0, 0, 0, 0),
        Position = UDim2.new(0.5, 0, 0.5, 0),
        AnchorPoint = Vector2.new(0.5, 0.5),
        ZIndex = 10
    })
    
    Create("UICorner", {Parent = ripple, CornerRadius = UDim.new(1, 0)})
    
    Tween(ripple, {Size = UDim2.new(2, 0, 2, 0), BackgroundTransparency = 1}, 0.5)
    game.Debris:AddItem(ripple, 0.5)
end

-- Main Library Constructor
function LunarUI.new(options)
    options = options or {}
    local self = setmetatable({}, LunarUI)
    
    self.Theme = options.Theme or "Dark"
    self.AccentColor = options.AccentColor or self.Themes[self.Theme].Accent
    self.Transparency = options.Transparency or 0
    self.AnimationSpeed = options.AnimationSpeed or 0.3
    self.Keybind = options.Keybind or Enum.KeyCode.RightControl
    self.CenterUI = options.CenterUI or true
    
    self.Open = false
    self.Windows = {}
    self.Elements = {}
    self.Notifications = {}
    
    self:CreateMainUI()
    self:CreateToggleButton()
    self:SetupKeybind()
    
    return self
end

function LunarUI:CreateMainUI()
    -- ScreenGui
    self.ScreenGui = Create("ScreenGui", {
        Name = "LunarUI",
        Parent = Player:WaitForChild("PlayerGui"),
        ResetOnSpawn = false,
        ZIndexBehavior = Enum.ZIndexBehavior.Global
    })
    
    -- Main Container (Centered)
    self.MainContainer = Create("Frame", {
        Name = "MainContainer",
        Parent = self.ScreenGui,
        Size = UDim2.new(0, 0, 0, 0),
        Position = UDim2.new(0.5, 0, 0.5, 0),
        BackgroundColor3 = self.Themes[self.Theme].Main,
        BackgroundTransparency = 1,
        AnchorPoint = Vector2.new(0.5, 0.5),
        ClipsDescendants = true,
        Visible = false
    })
    
    Create("UICorner", {Parent = self.MainContainer, CornerRadius = UDim.new(0, 15)})
    
    Create("UIStroke", {
        Parent = self.MainContainer,
        Color = self.AccentColor,
        Thickness = 2,
        Transparency = 0.5
    })
    
    -- Top Bar
    self.TopBar = Create("Frame", {
        Name = "TopBar",
        Parent = self.MainContainer,
        Size = UDim2.new(1, 0, 0, 45),
        BackgroundColor3 = self.Themes[self.Theme].Secondary,
        BackgroundTransparency = 0
    })
    
    Create("UICorner", {Parent = self.TopBar, CornerRadius = UDim.new(0, 12)})
    
    -- Title
    self.Title = Create("TextLabel", {
        Name = "Title",
        Parent = self.TopBar,
        Size = UDim2.new(0, 200, 1, 0),
        Position = UDim2.new(0, 15, 0, 0),
        BackgroundTransparency = 1,
        Text = "Lunar UI",
        TextColor3 = self.Themes[self.Theme].Text,
        TextSize = 20,
        Font = Enum.Font.GothamBold,
        TextXAlignment = Enum.TextXAlignment.Left
    })
    
    -- Close Button
    self.CloseButton = Create("TextButton", {
        Name = "CloseButton",
        Parent = self.TopBar,
        Size = UDim2.new(0, 35, 0, 35),
        Position = UDim2.new(1, -45, 0.5, -17.5),
        AnchorPoint = Vector2.new(1, 0.5),
        BackgroundColor3 = Color3.fromRGB(220, 60, 60),
        Text = "×",
        TextColor3 = Color3.fromRGB(255, 255, 255),
        TextSize = 20,
        Font = Enum.Font.GothamBold
    })
    
    Create("UICorner", {Parent = self.CloseButton, CornerRadius = UDim.new(1, 0)})
    
    self.CloseButton.MouseButton1Click:Connect(function()
        RippleEffect(self.CloseButton)
        self:ToggleUI()
    end)
    
    -- Content Container
    self.ContentContainer = Create("ScrollingFrame", {
        Name = "ContentContainer",
        Parent = self.MainContainer,
        Size = UDim2.new(1, -30, 1, -75),
        Position = UDim2.new(0, 15, 0, 60),
        BackgroundTransparency = 1,
        ScrollBarThickness = 4,
        ScrollBarImageColor3 = self.AccentColor,
        CanvasSize = UDim2.new(0, 0, 0, 0),
        AutomaticCanvasSize = Enum.AutomaticSize.Y
    })
    
    Create("UIListLayout", {
        Parent = self.ContentContainer,
        Padding = UDim.new(0, 12),
        SortOrder = Enum.SortOrder.LayoutOrder
    })
end

function LunarUI:CreateToggleButton()
    -- Toggle Button (Movable)
    self.ToggleButton = Create("TextButton", {
        Name = "ToggleButton",
        Parent = self.ScreenGui,
        Size = UDim2.new(0, 55, 0, 55),
        Position = UDim2.new(0, 20, 0.5, -27.5),
        BackgroundColor3 = self.AccentColor,
        Text = "☰",
        TextColor3 = Color3.fromRGB(255, 255, 255),
        TextSize = 26,
        Font = Enum.Font.GothamBold,
        ZIndex = 100
    })
    
    Create("UICorner", {Parent = self.ToggleButton, CornerRadius = UDim.new(1, 0)})
    
    Create("UIStroke", {
        Parent = self.ToggleButton,
        Color = Color3.fromRGB(255, 255, 255),
        Thickness = 2
    })
    
    -- Drop Shadow
    Create("ImageLabel", {
        Name = "Shadow",
        Parent = self.ToggleButton,
        Size = UDim2.new(1, 10, 1, 10),
        Position = UDim2.new(0.5, 0, 0.5, 0),
        AnchorPoint = Vector2.new(0.5, 0.5),
        BackgroundTransparency = 1,
        Image = "rbxassetid://5554236805",
        ImageColor3 = Color3.fromRGB(0, 0, 0),
        ImageTransparency = 0.7,
        ScaleType = Enum.ScaleType.Slice,
        SliceCenter = Rect.new(10, 10, 118, 118),
        ZIndex = 99
    })
    
    -- Make draggable
    local dragging, dragInput, dragStart, startPos
    
    local function Update(input)
        local delta = input.Position - dragStart
        self.ToggleButton.Position = UDim2.new(
            0, startPos.X.Offset + delta.X,
            0, startPos.Y.Offset + delta.Y
        )
    end
    
    self.ToggleButton.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = self.ToggleButton.Position
            
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    
    self.ToggleButton.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            Update(input)
        end
    end)
    
    -- Toggle UI
    self.ToggleButton.MouseButton1Click:Connect(function()
        RippleEffect(self.ToggleButton)
        self:ToggleUI()
    end)
    
    -- Hover effects
    self.ToggleButton.MouseEnter:Connect(function()
        Tween(self.ToggleButton, {Size = UDim2.new(0, 60, 0, 60)}, 0.2)
    end)
    
    self.ToggleButton.MouseLeave:Connect(function()
        Tween(self.ToggleButton, {Size = UDim2.new(0, 55, 0, 55)}, 0.2)
    end)
end

function LunarUI:SetupKeybind()
    UserInputService.InputBegan:Connect(function(input)
        if input.KeyCode == self.Keybind then
            self:ToggleUI()
        end
    end)
end

function LunarUI:ToggleUI()
    self.Open = not self.Open
    
    if self.Open then
        self.MainContainer.Visible = true
        Tween(self.MainContainer, {
            Size = UDim2.new(0, 520, 0, 420),
            BackgroundTransparency = self.Transparency
        }, self.AnimationSpeed, Enum.EasingStyle.Back, Enum.EasingDirection.Out)
        
        Tween(self.ToggleButton, {
            Rotation = 180,
            BackgroundColor3 = Color3.fromRGB(140, 100, 240)
        }, 0.3)
    else
        Tween(self.MainContainer, {
            Size = UDim2.new(0, 0, 0, 0),
            BackgroundTransparency = 1
        }, self.AnimationSpeed, Enum.EasingStyle.Back, Enum.EasingDirection.In)
        
        Tween(self.ToggleButton, {
            Rotation = 0,
            BackgroundColor3 = self.AccentColor
        }, 0.3)
        
        task.wait(self.AnimationSpeed)
        self.MainContainer.Visible = false
    end
end

-- Window System
function LunarUI:CreateWindow(options)
    options = options or {}
    local window = {
        Name = options.Name or "Window",
        Size = options.Size or UDim2.new(1, 0, 0, 320)
    }
    
    local windowFrame = Create("Frame", {
        Name = window.Name,
        Parent = self.ContentContainer,
        Size = window.Size,
        BackgroundColor3 = self.Themes[self.Theme].Secondary,
        BackgroundTransparency = 0,
        LayoutOrder = #self.Windows + 1
    })
    
    Create("UICorner", {Parent = windowFrame, CornerRadius = UDim.new(0, 10)})
    
    Create("UIStroke", {
        Parent = windowFrame,
        Color = self.AccentColor,
        Thickness = 1,
        Transparency = 0.3
    })
    
    local windowTitle = Create("TextLabel", {
        Parent = windowFrame,
        Size = UDim2.new(1, -20, 0, 35),
        Position = UDim2.new(0, 10, 0, 5),
        BackgroundTransparency = 1,
        Text = window.Name,
        TextColor3 = self.Themes[self.Theme].Text,
        TextSize = 17,
        Font = Enum.Font.GothamBold,
        TextXAlignment = Enum.TextXAlignment.Left
    })
    
    local windowContent = Create("ScrollingFrame", {
        Parent = windowFrame,
        Size = UDim2.new(1, -20, 1, -50),
        Position = UDim2.new(0, 10, 0, 45),
        BackgroundTransparency = 1,
        ScrollBarThickness = 3,
        ScrollBarImageColor3 = self.AccentColor,
        CanvasSize = UDim2.new(0, 0, 0, 0),
        AutomaticCanvasSize = Enum.AutomaticSize.Y
    })
    
    Create("UIListLayout", {
        Parent = windowContent,
        Padding = UDim.new(0, 10),
        SortOrder = Enum.SortOrder.LayoutOrder
    })
    
    window.Frame = windowFrame
    window.Content = windowContent
    table.insert(self.Windows, window)
    
    return window
end

-- UI Elements
function LunarUI:CreateButton(window, options)
    options = options or {}
    local button = {
        Name = options.Name or "Button",
        Callback = options.Callback or function() end
    }
    
    local buttonFrame = Create("Frame", {
        Parent = window.Content,
        Size = UDim2.new(1, 0, 0, 40),
        BackgroundColor3 = self.Themes[self.Theme].Main,
        LayoutOrder = #window.Content:GetChildren()
    })
    
    Create("UICorner", {Parent = buttonFrame, CornerRadius = UDim.new(0, 8)})
    
    Create("UIStroke", {
        Parent = buttonFrame,
        Color = self.AccentColor,
        Thickness = 1
    })
    
    local buttonText = Create("TextButton", {
        Parent = buttonFrame,
        Size = UDim2.new(1, 0, 1, 0),
        BackgroundTransparency = 1,
        Text = button.Name,
        TextColor3 = self.Themes[self.Theme].Text,
        TextSize = 15,
        Font = Enum.Font.Gotham
    })
    
    buttonText.MouseEnter:Connect(function()
        Tween(buttonFrame, {BackgroundColor3 = self.AccentColor}, 0.2)
        Tween(buttonText, {TextColor3 = Color3.fromRGB(255, 255, 255)}, 0.2)
    end)
    
    buttonText.MouseLeave:Connect(function()
        Tween(buttonFrame, {BackgroundColor3 = self.Themes[self.Theme].Main}, 0.2)
        Tween(buttonText, {TextColor3 = self.Themes[self.Theme].Text}, 0.2)
    end)
    
    buttonText.MouseButton1Click:Connect(function()
        RippleEffect(buttonFrame)
        Tween(buttonFrame, {Size = UDim2.new(0.98, 0, 0, 40)}, 0.1):Completed(function()
            Tween(buttonFrame, {Size = UDim2.new(1, 0, 0, 40)}, 0.1)
            button.Callback()
        end)
    end)
    
    return button
end

function LunarUI:CreateToggle(window, options)
    options = options or {}
    local toggle = {
        Name = options.Name or "Toggle",
        Default = options.Default or false,
        Callback = options.Callback or function() end,
        State = false
    }
    
    local toggleFrame = Create("Frame", {
        Parent = window.Content,
        Size = UDim2.new(1, 0, 0, 40),
        BackgroundColor3 = self.Themes[self.Theme].Main,
        LayoutOrder = #window.Content:GetChildren()
    })
    
    Create("UICorner", {Parent = toggleFrame, CornerRadius = UDim.new(0, 8)})
    
    Create("UIStroke", {
        Parent = toggleFrame,
        Color = self.AccentColor,
        Thickness = 1
    })
    
    local toggleText = Create("TextLabel", {
        Parent = toggleFrame,
        Size = UDim2.new(0.7, 0, 1, 0),
        BackgroundTransparency = 1,
        Text = toggle.Name,
        TextColor3 = self.Themes[self.Theme].Text,
        TextSize = 15,
        Font = Enum.Font.Gotham,
        TextXAlignment = Enum.TextXAlignment.Left
    })
    
    local toggleButton = Create("Frame", {
        Parent = toggleFrame,
        Size = UDim2.new(0, 55, 0, 28),
        Position = UDim2.new(1, -65, 0.5, -14),
        AnchorPoint = Vector2.new(1, 0.5),
        BackgroundColor3 = Color3.fromRGB(70, 70, 80)
    })
    
    Create("UICorner", {Parent = toggleButton, CornerRadius = UDim.new(1, 0)})
    
    local toggleCircle = Create("Frame", {
        Parent = toggleButton,
        Size = UDim2.new(0, 22, 0, 22),
        Position = UDim2.new(0, 3, 0.5, -11),
        AnchorPoint = Vector2.new(0, 0.5),
        BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    })
    
    Create("UICorner", {Parent = toggleCircle, CornerRadius = UDim.new(1, 0)})
    
    local function UpdateToggle()
        if toggle.State then
            Tween(toggleButton, {BackgroundColor3 = self.AccentColor}, 0.2)
            Tween(toggleCircle, {Position = UDim2.new(1, -25, 0.5, -11)}, 0.2)
            toggleText.Text = toggle.Name .. ": ON"
        else
            Tween(toggleButton, {BackgroundColor3 = Color3.fromRGB(70, 70, 80)}, 0.2)
            Tween(toggleCircle, {Position = UDim2.new(0, 3, 0.5, -11)}, 0.2)
            toggleText.Text = toggle.Name .. ": OFF"
        end
    end
    
    local function ToggleFunc()
        toggle.State = not toggle.State
        UpdateToggle()
        RippleEffect(toggleButton)
        toggle.Callback(toggle.State)
    end
    
    toggleButton.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            ToggleFunc()
        end
    end)
    
    toggleText.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            ToggleFunc()
        end
    end)
    
    toggle.State = toggle.Default
    UpdateToggle()
    
    return toggle
end

function LunarUI:CreateSlider(window, options)
    options = options or {}
    local slider = {
        Name = options.Name or "Slider",
        Min = options.Min or 0,
        Max = options.Max or 100,
        Default = options.Default or 50,
        Callback = options.Callback or function() end,
        Value = 50
    }
    
    local sliderFrame = Create("Frame", {
        Parent = window.Content,
        Size = UDim2.new(1, 0, 0, 55),
        BackgroundColor3 = self.Themes[self.Theme].Main,
        LayoutOrder = #window.Content:GetChildren()
    })
    
    Create("UICorner", {Parent = sliderFrame, CornerRadius = UDim.new(0, 8)})
    
    Create("UIStroke", {
        Parent = sliderFrame,
        Color = self.AccentColor,
        Thickness = 1
    })
    
    local sliderText = Create("TextLabel", {
        Parent = sliderFrame,
        Size = UDim2.new(1, -20, 0, 20),
        Position = UDim2.new(0, 10, 0, 5),
        BackgroundTransparency = 1,
        Text = slider.Name .. ": " .. slider.Default,
        TextColor3 = self.Themes[self.Theme].Text,
        TextSize = 15,
        Font = Enum.Font.Gotham,
        TextXAlignment = Enum.TextXAlignment.Left
    })
    
    local sliderBar = Create("Frame", {
        Parent = sliderFrame,
        Size = UDim2.new(1, -20, 0, 6),
        Position = UDim2.new(0, 10, 1, -25),
        AnchorPoint = Vector2.new(0, 1),
        BackgroundColor3 = Color3.fromRGB(60, 60, 70)
    })
    
    Create("UICorner", {Parent = sliderBar, CornerRadius = UDim.new(1, 0)})
    
    local sliderFill = Create("Frame", {
        Parent = sliderBar,
        Size = UDim2.new((slider.Default - slider.Min) / (slider.Max - slider.Min), 0, 1, 0),
        BackgroundColor3 = self.AccentColor
    })
    
    Create("UICorner", {Parent = sliderFill, CornerRadius = UDim.new(1, 0)})
    
    local sliderButton = Create("TextButton", {
        Parent = sliderBar,
        Size = UDim2.new(0, 18, 0, 18),
        Position = UDim2.new(sliderFill.Size.X.Scale, -9, 0.5, -9),
        AnchorPoint = Vector2.new(0, 0.5),
        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
        Text = "",
        AutoButtonColor = false
    })
    
    Create("UICorner", {Parent = sliderButton, CornerRadius = UDim.new(1, 0)})
    
    local function UpdateSlider(value)
        local percent = math.clamp((value - slider.Min) / (slider.Max - slider.Min), 0, 1)
        slider.Value = math.floor(value)
        sliderText.Text = slider.Name .. ": " .. slider.Value
        Tween(sliderFill, {Size = UDim2.new(percent, 0, 1, 0)}, 0.1)
        Tween(sliderButton, {Position = UDim2.new(percent, -9, 0.5, -9)}, 0.1)
        slider.Callback(slider.Value)
    end
    
    local dragging = false
    
    sliderButton.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
        end
    end)
    
    sliderButton.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    
    sliderBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            local percent = (input.Position.X - sliderBar.AbsolutePosition.X) / sliderBar.AbsoluteSize.X
            UpdateSlider(slider.Min + percent * (slider.Max - slider.Min))
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local percent = (input.Position.X - sliderBar.AbsolutePosition.X) / sliderBar.AbsoluteSize.X
            UpdateSlider(slider.Min + math.clamp(percent, 0, 1) * (slider.Max - slider.Min))
        end
    end)
    
    UpdateSlider(slider.Default)
    
    return slider
end

function LunarUI:CreateLabel(window, text)
    local label = Create("TextLabel", {
        Parent = window.Content,
        Size = UDim2.new(1, 0, 0, 25),
        BackgroundTransparency = 1,
        Text = text,
        TextColor3 = self.Themes[self.Theme].Text,
        TextSize = 14,
        Font = Enum.Font.Gotham,
        TextXAlignment = Enum.TextXAlignment.Left,
        LayoutOrder = #window.Content:GetChildren()
    })
    
    return label
end

function LunarUI:CreateSection(window, name)
    local section = Create("Frame", {
        Parent = window.Content,
        Size = UDim2.new(1, 0, 0, 30),
        BackgroundTransparency = 1,
        LayoutOrder = #window.Content:GetChildren()
    })
    
    local line = Create("Frame", {
        Parent = section,
        Size = UDim2.new(1, 0, 0, 1),
        Position = UDim2.new(0, 0, 0.5, 0),
        BackgroundColor3 = self.AccentColor,
        BackgroundTransparency = 0.5
    })
    
    local text = Create("TextLabel", {
        Parent = section,
        Size = UDim2.new(0, 100, 0, 22),
        Position = UDim2.new(0.5, -50, 0.5, -11),
        AnchorPoint = Vector2.new(0.5, 0.5),
        BackgroundColor3 = self.Themes[self.Theme].Main,
        Text = name,
        TextColor3 = self.AccentColor,
        TextSize = 14,
        Font = Enum.Font.GothamBold
    })
    
    Create("UICorner", {Parent = text, CornerRadius = UDim.new(0, 4)})
    
    return section
end

-- Notification System
function LunarUI:Notify(options)
    options = options or {}
    
    local notification = Create("Frame", {
        Parent = self.ScreenGui,
        Size = UDim2.new(0, 320, 0, 85),
        Position = UDim2.new(1, 350, 1, -100),
        AnchorPoint = Vector2.new(1, 1),
        BackgroundColor3 = self.Themes[self.Theme].Main,
        BackgroundTransparency = 1
    })
    
    Create("UICorner", {Parent = notification, CornerRadius = UDim.new(0, 10)})
    
    Create("UIStroke", {
        Parent = notification,
        Color = self.AccentColor,
        Thickness = 2
    })
    
    local inner = Create("Frame", {
        Parent = notification,
        Size = UDim2.new(1, -4, 1, -4),
        Position = UDim2.new(0, 2, 0, 2),
        BackgroundColor3 = self.Themes[self.Theme].Secondary
    })
    
    Create("UICorner", {Parent = inner, CornerRadius = UDim.new(0, 8)})
    
    local title = Create("TextLabel", {
        Parent = inner,
        Size = UDim2.new(1, -20, 0, 25),
        Position = UDim2.new(0, 10, 0, 8),
        BackgroundTransparency = 1,
        Text = options.Title or "Notification",
        TextColor3 = self.AccentColor,
        TextSize = 17,
        Font = Enum.Font.GothamBold,
        TextXAlignment = Enum.TextXAlignment.Left
    })
    
    local content = Create("TextLabel", {
        Parent = inner,
        Size = UDim2.new(1, -20, 1, -40),
        Position = UDim2.new(0, 10, 0, 35),
        BackgroundTransparency = 1,
        Text = options.Text or "This is a notification",
        TextColor3 = self.Themes[self.Theme].Text,
        TextSize = 14,
        Font = Enum.Font.Gotham,
        TextXAlignment = Enum.TextXAlignment.Left,
        TextWrapped = true
    })
    
    -- Show animation
    Tween(notification, {
        Position = UDim2.new(1, -20, 1, -100),
        BackgroundTransparency = 0
    }, 0.5, Enum.EasingStyle.Back)
    
    -- Auto remove
    task.spawn(function()
        task.wait(options.Duration or 5)
        Tween(notification, {
            Position = UDim2.new(1, 350, 1, -100),
            BackgroundTransparency = 1
        }, 0.5, Enum.EasingStyle.Back, Enum.EasingDirection.In):Completed(function()
            notification:Destroy()
        end)
    end)
    
    -- Click to dismiss
    notification.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            Tween(notification, {
                Position = UDim2.new(1, 350, 1, -100),
                BackgroundTransparency = 1
            }, 0.3):Completed(function()
                notification:Destroy()
            end)
        end
    end)
end

-- Watermark
function LunarUI:AddWatermark()
    local watermark = Create("TextLabel", {
        Parent = self.ScreenGui,
        Size = UDim2.new(0, 220, 0, 32),
        Position = UDim2.new(0, 10, 0, 10),
        BackgroundColor3 = self.Themes[self.Theme].Main,
        BackgroundTransparency = 0.5,
        Text = "Lunar UI v3.0 | FPS: 60",
        TextColor3 = self.Themes[self.Theme].Text,
        TextSize = 14,
        Font = Enum.Font.Gotham,
        TextXAlignment = Enum.TextXAlignment.Left
    })
    
    Create("UICorner", {Parent = watermark, CornerRadius = UDim.new(0, 6)})
    
    Create("UIStroke", {
        Parent = watermark,
        Color = self.AccentColor,
        Thickness = 1
    })
    
    -- FPS Counter
    local fps = 0
    local frames = 0
    local lastTime = tick()
    
    RunService.Heartbeat:Connect(function()
        frames = frames + 1
        local currentTime = tick()
        if currentTime - lastTime >= 1 then
            fps = frames
            frames = 0
            lastTime = currentTime
            watermark.Text = "Lunar UI v3.0 | FPS: " .. fps
        end
    end)
    
    return watermark
end

-- Destroy
function LunarUI:Destroy()
    self.ScreenGui:Destroy()
end

return LunarUI
