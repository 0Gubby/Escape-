-- [[ 1. INITIALIZE MODDED LOADER CORES ]]
local Fluent = loadstring(game:HttpGet("https://github.com/StyearX/Fluent-Modded/releases/download/Fluent/FluentPro"))()

local Players = game:GetService("Players")
local HttpService = game:GetService("HttpService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

local Tabs = {}

local function Notify(title, content, ntype, icon, duration)
    Fluent:Notify({Title = title, Content = content, Type = ntype or "Info", Icon = icon, Duration = duration or 3})
end

local function RunScript(url)
    local success, err = pcall(function()
        loadstring(game:HttpGet(url))()
    end)
    if not success then
        warn("[Gubby Hub Error]: " .. tostring(err))
    end
end

-- =========================================================================
-- THEME CONFIGURATIONS (NEONBLUE COMPACT PRESET)
-- =========================================================================
Fluent:RegisterCustomTheme("NeonBlue", {
    Accent = Color3.fromRGB(0, 212, 191),
    AcrylicMain = Color3.fromRGB(13, 15, 22),       
    AcrylicBorder = Color3.fromRGB(40, 48, 64),
    AcrylicGradient = ColorSequence.new(Color3.fromRGB(13, 15, 22), Color3.fromRGB(18, 21, 30)),
    AcrylicNoise = 0.6,
    TitleBarLine = Color3.fromRGB(40, 48, 64),
    Tab = Color3.fromRGB(18, 21, 30),
    Element = Color3.fromRGB(24, 28, 38),
    ElementBorder = Color3.fromRGB(40, 48, 64),
    InElementBorder = Color3.fromRGB(32, 38, 52),
    ElementTransparency = 0.85,
    ToggleSlider = Color3.fromRGB(32, 38, 52),
    ToggleToggled = Color3.fromRGB(0, 212, 191),
    SliderRail = Color3.fromRGB(32, 38, 52),
    DropdownFrame = Color3.fromRGB(18, 21, 30),
    DropdownHolder = Color3.fromRGB(13, 15, 22),
    DropdownBorder = Color3.fromRGB(40, 48, 64),
    DropdownOption = Color3.fromRGB(24, 28, 38),
    Keybind = Color3.fromRGB(24, 28, 38),
    Input = Color3.fromRGB(24, 28, 38),
    InputFocused = Color3.fromRGB(18, 21, 30),
    InputIndicator = Color3.fromRGB(0, 212, 191),
    Dialog = Color3.fromRGB(18, 21, 30),
    DialogHolder = Color3.fromRGB(13, 15, 22),
    DialogHolderLine = Color3.fromRGB(40, 48, 64),
    DialogButton = Color3.fromRGB(24, 28, 38),
    DialogButtonBorder = Color3.fromRGB(40, 48, 64),
    DialogBorder = Color3.fromRGB(40, 48, 64),
    DialogInput = Color3.fromRGB(24, 28, 38),
    DialogInputLine = Color3.fromRGB(0, 212, 191),
    Text = Color3.fromRGB(245, 247, 250),
    SubText = Color3.fromRGB(135, 145, 162),
    Hover = Color3.fromRGB(32, 38, 52),
    HoverChange = 0.05,
    ShineEnabled = true,
    Shine = {
        Speed = 0.5,
        RotationSpeed = 25,
        ColorSequence = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(40, 48, 64)),
            ColorSequenceKeypoint.new(0.5, Color3.fromRGB(0, 212, 191)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(40, 48, 64)),
        }),
    },
    StrokeShine = true,
    StrokeDark = Color3.fromRGB(40, 48, 64),
    ButtonGradient = {
        Background = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(24, 28, 38)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(18, 21, 30)),
        }),
        Stroke = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(40, 48, 64)),
            ColorSequenceKeypoint.new(0.5, Color3.fromRGB(0, 212, 191)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(40, 48, 64)),
        }),
    },
})

-- [[ 3. CREATE INITIAL WINDOW OVERLAY WITH EXPANDED X-AXIS FOR TEXT ]]
local Window = Fluent:CreateWindow({
    Title = "GUBBY TEAM HUB",
    SubTitle = "by Gubby_scripter",
    TabWidth = 135,                    -- Expanded tab strip area width to prevent text overflow
    Size = UDim2.fromOffset(500, 390), -- Increased panel horizontal width to 500 pixels so profile name displays fully
    Acrylic = false,                   
    Theme = "NeonBlue",
    MinimizeKey = Enum.KeyCode.LeftControl,
    Search = false, 
    Icons = "solar/planet-bold",
    TitleIcon = "solar/planet-bold",
    UserInfoTop = true, 
    UserInfoTitle = LocalPlayer.DisplayName or LocalPlayer.Name
})

-- [[ 4. SCRIPT ENTRYS ROUTER ASSIGNMENTS ]]
Tabs.Scripts = Window:AddTab({ Title = "Scripts", Icon = "code" })

-- =========================================================================
-- PLACING SEARCH COMPONENT AT THE VERY TOP OF THE SCRIPTS ELEMENT ORDER
-- =========================================================================
local searchContainer = {}

Tabs.Scripts:AddInput("ScriptSearch", {
    Title = "Search Scripts",
    Placeholder = "Type game name to filter...",
    Numeric = false,
    Finished = false,
    Callback = function(Value)
        local query = string.lower(Value)
        for _, item in ipairs(searchContainer) do
            if query == "" or string.find(string.lower(item.Name), query) then
                item.Element.Frame.Visible = true
                if item.ImageLabel then item.ImageLabel.Visible = true end
            else
                item.Element.Frame.Visible = false
                if item.ImageLabel then item.ImageLabel.Visible = false end
            end
        end
    end
})

Tabs.Scripts:AddParagraph({
    Title = "Available Exploits",
    Content = "Select a game variant below to run automated functions."
})

-- =========================================================================
-- IMAGE CARD CREATION LOGIC WITH AUTO-ARRANGEMENT HANDLERS
-- =========================================================================
local function addGameShortcutWithCard(name, url, universeId)
    local btn = Tabs.Scripts:AddButton({
        Title = name,
        Description = "Tap to execute code matrix safely",
        Callback = function()
            RunScript(url)
            Notify("Executing", "Loading entry script data for " .. name, "Info", "code", 2)
        end
    })

    local targetContainer = btn.Frame and btn.Frame.Parent
    local imgLabel = nil
    
    if targetContainer then
        imgLabel = Instance.new("ImageLabel")
        imgLabel.Size = UDim2.new(1, -20, 0, 85) 
        imgLabel.BackgroundColor3 = Color3.fromRGB(24, 28, 38)
        imgLabel.BorderSizePixel = 0
        imgLabel.ClipsDescendants = true
        
        if btn.Frame:IsA("GuiObject") then
            -- Layout assignment ensures images lock natively direct above corresponding buttons
            imgLabel.LayoutOrder = btn.Frame.LayoutOrder - 1
        end
        
        if universeId and type(universeId) == "number" then
            imgLabel.Image = "https://www.roblox.com/asset-thumbnail/image?assetId=" .. tostring(universeId) .. "&width=768&height=432&format=png"
        else
            imgLabel.Image = "rbxassetid://12543132717" 
        end
        
        imgLabel.ScaleType = Enum.ScaleType.Crop
        imgLabel.Parent = targetContainer
        
        local round = Instance.new("UICorner")
        round.CornerRadius = UDim.new(0, 6)
        round.Parent = imgLabel
        
        local stroke = Instance.new("UIStroke")
        stroke.Color = Color3.fromRGB(40, 48, 64)
        stroke.Thickness = 1
        stroke.Parent = imgLabel
    end

    table.insert(searchContainer, {
        Name = name,
        Element = btn,
        ImageLabel = imgLabel
    })
end

-- Injecting entry elements into the layout queue directly below the search header
addGameShortcutWithCard("Fårup Sommerland", "https://pastefy.app/oyr9IO6j/raw", 100084571809362)
addGameShortcutWithCard("Hex Mall", "https://pastefy.app/zWMuL9zp/raw", 131807993575629)
addGameShortcutWithCard("Extra Easy Obby", "https://pastefy.app/qvoiJPW9/raw", nil)
addGameShortcutWithCard("Cute Pets UGC", "https://pastefy.app/mZEFVGnv/raw", nil)
addGameShortcutWithCard("Fake Purchase", "https://pastefy.app/Tnz2lNSl/raw", nil)
addGameShortcutWithCard("Whyborn", "https://pastefy.app/lND8cWtT/raw", nil)
addGameShortcutWithCard("Free UGC Obby", "https://pastefy.app/71IJXOor/raw", nil)
addGameShortcutWithCard("Carrie Adventure", "https://pastefy.app/GkknKVDn/raw", nil)
addGameShortcutWithCard("Tower of Misery", "https://pastefy.app/esahIVoc/raw", nil)
addGameShortcutWithCard("Ugc Obby AFK", "https://pastefy.app/LqwJVwEN/raw", nil)
addGameShortcutWithCard("Shopping Spree", "https://pastefy.app/dM6G4Dee/raw", nil)
addGameShortcutWithCard("Get Your Avatar", "https://pastefy.app/JCfsGtHm/raw", 93100091331318)
addGameShortcutWithCard("Slimes Battles (FREE UGC)", "https://pastefy.app/gpt20BQb/raw", 116236811476343)
addGameShortcutWithCard("[FREE UGC]🍌AVL:The Show 2", "https://pastefy.app/BcWxPJr9/raw", 138979827505199)

-- [[ 5. FEEDBACK INTEGRATION INTERFACE ]]
Tabs.Feedback = Window:AddTab({ Title = "Feedback", Icon = "mail" })

local feedbackInput = ""
Tabs.Feedback:AddInput("FeedbackBox", {
    Title = "Message Body",
    Placeholder = "Type system messages or bugs here...",
    Numeric = false,
    Finished = false,
    Callback = function(Value)
        feedbackInput = Value
    end
})

local WEBHOOK_URL = "https://discord.com/api/webhooks/1515737371486851213/pgvsX7v7hpO1y7hX3BJQRBMDinnlYEQkWTuSQNTig3SRY_y_fZ2Tox7O4Kj_jN66NROS"

Tabs.Feedback:AddButton({
    Title = "SUBMIT FEEDBACK",
    Description = "Routes directly to system webhook logs",
    Callback = function()
        if feedbackInput == "" then 
            Notify("Error", "Message body context empty!", "Error", "alert-triangle", 3)
            return 
        end
        
        Notify("Sending", "Delivering logs to developers...", "Info", "send", 2)
        
        local payload = {
            ["username"] = "Gubby Team Hub Feedback",
            ["embeds"] = {{["title"] = "📩 Feedback", ["description"] = feedbackInput, ["color"] = 52735}}
        }
        
        task.spawn(function()
            local success, jsonPayload = pcall(function() return HttpService:JSONEncode(payload) end)
            if not success then return end
            
            pcall(function()
                local reqFunc = request or (syn and syn.request) or (http and http.request)
                if reqFunc then
                    reqFunc({
                        Url = WEBHOOK_URL,
                        Method = "POST",
                        Headers = {["Content-Type"] = "application/json"},
                        Body = jsonPayload
                    })
                else
                    HttpService:PostAsync(WEBHOOK_URL, jsonPayload, Enum.HttpContentType.ApplicationJson)
                end
                Notify("Delivered", "Feedback system confirmation updated.", "Info", "check", 3)
            end)
        end)
    end
})

-- =========================================================================
-- DRAGGABLE FLOATING MOBILE INTERFACE TOGGLE MINIMIZE BUTTON
-- =========================================================================
local CoreGui = game:GetService("CoreGui")
local TargetParent = CoreGui:FindFirstChild("RobloxGui") or LocalPlayer:FindFirstChildOfClass("PlayerGui")

if TargetParent:FindFirstChild("GubbyMobileMinimize") then
    TargetParent.GubbyMobileMinimize:Destroy()
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "GubbyMobileMinimize"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = TargetParent

local MinimizeBtn = Instance.new("TextButton")
MinimizeBtn.Name = "Toggle"
MinimizeBtn.Size = UDim2.fromOffset(50, 50)
MinimizeBtn.Position = UDim2.new(0.05, 0, 0.25, 0)
MinimizeBtn.BackgroundColor3 = Color3.fromRGB(13, 15, 22)
MinimizeBtn.TextColor3 = Color3.fromRGB(0, 212, 191)
MinimizeBtn.TextSize = 12
MinimizeBtn.Font = Enum.Font.GothamBold
MinimizeBtn.Text = "MENU"
MinimizeBtn.Parent = ScreenGui

local UIStroke = Instance.new("UIStroke")
UIStroke.Color = Color3.fromRGB(40, 48, 64)
UIStroke.Thickness = 2
UIStroke.Parent = MinimizeBtn

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 12)
UICorner.Parent = MinimizeBtn

MinimizeBtn.MouseButton1Click:Connect(function()
    Window:Minimize()
end)

local dragging, dragInput, dragStart, startPos
local function update(input)
    local delta = input.Position - dragStart
    MinimizeBtn.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

MinimizeBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = MinimizeBtn.Position
        
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

MinimizeBtn.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        update(input)
    end
end)

Window:SelectTab(1)
Notify("Script Loaded", "Resolution optimized. Top-level search field deployed.", "Info", "solar/planet-bold", 5)
