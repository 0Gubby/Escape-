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
local UIColors = {
    MainBG = Color3.fromRGB(13, 15, 22),
    ElementBG = Color3.fromRGB(24, 28, 38),
    Border = Color3.fromRGB(40, 48, 64),
    Accent = Color3.fromRGB(0, 212, 191),
    Text = Color3.fromRGB(245, 247, 250),
    SubText = Color3.fromRGB(135, 145, 162)
}

Fluent:RegisterCustomTheme("NeonBlue", {
    Accent = UIColors.Accent,
    AcrylicMain = UIColors.MainBG,       
    AcrylicBorder = UIColors.Border,
    AcrylicGradient = ColorSequence.new(UIColors.MainBG, Color3.fromRGB(18, 21, 30)),
    AcrylicNoise = 0.6,
    TitleBarLine = UIColors.Border,
    Tab = Color3.fromRGB(18, 21, 30),
    Element = UIColors.ElementBG,
    ElementBorder = UIColors.Border,
    InElementBorder = Color3.fromRGB(32, 38, 52),
    ElementTransparency = 0.85,
    ToggleSlider = Color3.fromRGB(32, 38, 52),
    ToggleToggled = UIColors.Accent,
    SliderRail = Color3.fromRGB(32, 38, 52),
    DropdownFrame = Color3.fromRGB(18, 21, 30),
    DropdownHolder = UIColors.MainBG,
    DropdownBorder = UIColors.Border,
    DropdownOption = UIColors.ElementBG,
    Keybind = UIColors.ElementBG,
    Input = UIColors.ElementBG,
    InputFocused = Color3.fromRGB(18, 21, 30),
    InputIndicator = UIColors.Accent,
    Dialog = Color3.fromRGB(18, 21, 30),
    DialogHolder = UIColors.MainBG,
    DialogHolderLine = UIColors.Border,
    DialogButton = UIColors.ElementBG,
    DialogButtonBorder = UIColors.Border,
    DialogBorder = UIColors.Border,
    DialogInput = UIColors.ElementBG,
    DialogInputLine = UIColors.Accent,
    Text = UIColors.Text,
    SubText = UIColors.SubText,
    Hover = Color3.fromRGB(32, 38, 52),
    HoverChange = 0.05,
    ShineEnabled = true,
    Shine = {
        Speed = 0.5,
        RotationSpeed = 25,
        ColorSequence = ColorSequence.new({
            ColorSequenceKeypoint.new(0, UIColors.Border),
            ColorSequenceKeypoint.new(0.5, UIColors.Accent),
            ColorSequenceKeypoint.new(1, UIColors.Border),
        }),
    },
    StrokeShine = true,
    StrokeDark = UIColors.Border,
    ButtonGradient = {
        Background = ColorSequence.new({
            ColorSequenceKeypoint.new(0, UIColors.ElementBG),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(18, 21, 30)),
        }),
        Stroke = ColorSequence.new({
            ColorSequenceKeypoint.new(0, UIColors.Border),
            ColorSequenceKeypoint.new(0.5, UIColors.Accent),
            ColorSequenceKeypoint.new(1, UIColors.Border),
        }),
    },
})

local CustomIconURL = "https://www.roblox.com/asset-thumbnail/image?assetId=103744621667632&width=420&height=420&format=png"

-- [[ 3. CREATE WINDOW ]]
local Window = Fluent:CreateWindow({
    Title = "GUBBY TEAM HUB",
    SubTitle = "by Gubby_scripter",
    TabWidth = 135,                    
    Size = UDim2.fromOffset(500, 390), 
    Acrylic = false,                   
    Theme = "NeonBlue",
    MinimizeKey = Enum.KeyCode.LeftControl,
    Search = false, 
    Icons = CustomIconURL,       
    TitleIcon = CustomIconURL,   
    UserInfoTop = true, 
    UserInfoTitle = LocalPlayer.DisplayName or LocalPlayer.Name
})

-- [[ 4. SCRIPT ENTRYS ROUTER ASSIGNMENTS ]]
Tabs.Scripts = Window:AddTab({ Title = "Scripts", Icon = "code" })

-- Forward declare the ScreenGui reference so the button can clean up the mobile toggle button too
local ScreenGui = nil

-- =========================================================================
-- IMAGE CARD GENERATION & FULL CLEANUP LOGIC
-- =========================================================================
local function addGameShortcutWithCard(name, url, universeId)
    local btn = Tabs.Scripts:AddButton({
        Title = "Execute Script",
        Description = "Game: " .. name,
        Callback = function()
            -- 1. Run script payload first
            RunScript(url)
            Notify("Executing", "Loading entry script data for " .. name, "Info", "code", 2)
            
            -- 2. Completely delete the mobile button container if it exists
            if ScreenGui then
                ScreenGui:Destroy()
            end
            
            -- 3. Completely delete the main GUI Hub window from existence
            Window:Destroy() 
        end
    })

    local targetFrame = btn.Frame
    if targetFrame and targetFrame:IsA("GuiObject") then
        targetFrame.Size = UDim2.new(targetFrame.Size.X.Scale, targetFrame.Size.X.Offset, 0, 145)
        
        local imgLabel = Instance.new("ImageLabel")
        imgLabel.Name = "CardPreviewImage"
        imgLabel.Size = UDim2.new(1, -20, 0, 85) 
        imgLabel.Position = UDim2.new(0, 10, 0, 50) 
        imgLabel.BackgroundColor3 = UIColors.MainBG
        imgLabel.BorderSizePixel = 0
        imgLabel.ClipsDescendants = true
        
        if universeId and type(universeId) == "number" then
            imgLabel.Image = "https://www.roblox.com/asset-thumbnail/image?assetId=" .. tostring(universeId) .. "&width=768&height=432&format=png"
        else
            imgLabel.Image = "rbxassetid://126071415712760" 
        end
        
        imgLabel.ScaleType = Enum.ScaleType.Crop
        imgLabel.Parent = targetFrame
        
        local round = Instance.new("UICorner")
        round.CornerRadius = UDim.new(0, 6)
        round.Parent = imgLabel
        
        local stroke = Instance.new("UIStroke")
        stroke.Color = UIColors.Border
        stroke.Thickness = 1
        stroke.Parent = imgLabel
    end
end

-- Appending lists down structural scroll frame queues
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
    Placeholder = "Type message here",
    Numeric = false,
    Finished = false,
    Callback = function(Value)
        feedbackInput = Value
    end
})

local WEBHOOK_URL = "https://discord.com/api/webhooks/1515737371486851213/pgvsX7v7hpO1y7hX3BJQRBMDinnlYEQkWTuSQNTig3SRY_y_fZ2Tox7O4Kj_jN66NROS"

Tabs.Feedback:AddButton({
    Title = "SUBMIT FEEDBACK",
    Description = "sent message to feedback channels in discord",
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

ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "GubbyMobileMinimize"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = TargetParent

local MinimizeBtn = Instance.new("TextButton")
MinimizeBtn.Name = "Toggle"
MinimizeBtn.Size = UDim2.fromOffset(50, 50)
MinimizeBtn.Position = UDim2.new(0.05, 0, 0.25, 0)
MinimizeBtn.BackgroundColor3 = UIColors.MainBG
MinimizeBtn.TextColor3 = UIColors.Accent
MinimizeBtn.TextSize = 24  
MinimizeBtn.Font = Enum.Font.GothamBold
MinimizeBtn.Text = "🐰"  
MinimizeBtn.Parent = ScreenGui

local UIStroke = Instance.new("UIStroke")
UIStroke.Color = UIColors.Border
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
Notify("Script Loaded", "Welcome to Gubby Team Hub.", "Info", "code", 5)
