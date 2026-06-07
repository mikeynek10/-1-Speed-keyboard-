local Players, UIS, CoreGui, VU, RunService, HttpService = game:GetService("Players"), game:GetService("UserInputService"), game:GetService("CoreGui"), game:GetService("VirtualUser"), game:GetService("RunService"), game:GetService("HttpService")
local Player = Players.LocalPlayer
local ParentGui = pcall(function() return CoreGui.Name end) and CoreGui or Player:WaitForChild("PlayerGui")

if ParentGui:FindFirstChild("MikeySpeedHub") then ParentGui["MikeySpeedHub"]:Destroy() end

-- ==========================================
-- CẤU HÌNH (LƯU / TẢI CHẠY NGẦM)
-- ==========================================
local ConfigFile = "MikeySpeedHub_Config.json"
_G.WalkSpeed, _G.InfJump, _G.EnableSpeed = 16, false, false

local function saveConfig()
    local data = {WalkSpeed = _G.WalkSpeed, InfJump = _G.InfJump, EnableSpeed = _G.EnableSpeed}
    if writefile then pcall(function() writefile(ConfigFile, HttpService:JSONEncode(data)) end) end
end

local function loadConfig()
    if readfile and isfile and isfile(ConfigFile) then
        local success, data = pcall(function() return HttpService:JSONDecode(readfile(ConfigFile)) end)
        if success and data then
            _G.WalkSpeed = data.WalkSpeed or 16
            _G.InfJump = data.InfJump or false
            _G.EnableSpeed = data.EnableSpeed or false
        end
    end
end
loadConfig()

-- ==========================================
-- GIAO DIỆN (UI)
-- ==========================================
local function cre(cls, parent, props)
    local inst = Instance.new(cls)
    if props then for k,v in pairs(props) do inst[k] = v end end
    if parent then inst.Parent = parent end
    return inst
end

local ScreenGui = cre("ScreenGui", ParentGui, {Name = "MikeySpeedHub", ResetOnSpawn = false})
local TopBar = cre("TextButton", ScreenGui, {Name = "TopBarToggle", Size = UDim2.new(0, 160, 0, 28), Position = UDim2.new(0.5, -80, 0, 5), BackgroundColor3 = Color3.fromRGB(20, 20, 25), Text = "⚡ Click để Hiện UI", TextColor3 = Color3.fromRGB(255, 255, 255), Font = Enum.Font.GothamBold, TextSize = 11, AutoButtonColor = false, Visible = false})
cre("UICorner", TopBar, {CornerRadius = UDim.new(0, 6)})
cre("UIStroke", TopBar, {Color = Color3.fromRGB(74, 77, 240), Thickness = 1.5})

local MainFrame = cre("Frame", ScreenGui, {Name = "MainFrame", Size = UDim2.new(0, 460, 0, 320), Position = UDim2.new(0.5, -230, 0.5, -160), BackgroundColor3 = Color3.fromRGB(25, 25, 30), Active = true, Draggable = true, Visible = true})
cre("UICorner", MainFrame, {CornerRadius = UDim.new(0, 10)})
cre("UIStroke", MainFrame, {Color = Color3.fromRGB(74, 77, 240), Thickness = 1.8})

-- THÊM LẠI HÌNH NỀN
local BackgroundImage = cre("ImageLabel", MainFrame, {
    Size = UDim2.new(1, 0, 1, 0),
    BackgroundTransparency = 1,
    Image = "rbxassetid://124118268887026", 
    ImageTransparency = 0.65, 
    ScaleType = Enum.ScaleType.Crop,
    ZIndex = 1 
})

local function toggleUI(v) MainFrame.Visible = v; TopBar.Visible = not v end
TopBar.MouseButton1Click:Connect(function() toggleUI(true) end)

local Header = cre("Frame", MainFrame, {Size = UDim2.new(1, 0, 0, 38), BackgroundTransparency = 1, ZIndex = 2})
cre("TextLabel", Header, {Size = UDim2.new(0.8, 0, 1, 0), Position = UDim2.new(0, 15, 0, 0), BackgroundTransparency = 1, Text = "+1 speed keyboard ( MIKEY )", TextColor3 = Color3.fromRGB(255, 255, 255), Font = Enum.Font.GothamBold, TextSize = 14, TextXAlignment = Enum.TextXAlignment.Left, ZIndex = 2})
local CloseBtn = cre("TextButton", Header, {Size = UDim2.new(0, 26, 0, 26), Position = UDim2.new(1, -35, 0.5, -13), BackgroundColor3 = Color3.fromRGB(35, 35, 45), Text = "✕", TextColor3 = Color3.fromRGB(230, 230, 230), Font = Enum.Font.GothamBold, TextSize = 12, AutoButtonColor = false, ZIndex = 3})
cre("UICorner", CloseBtn, {CornerRadius = UDim.new(0, 6)})
CloseBtn.MouseButton1Click:Connect(function() toggleUI(false) end)
UIS.InputBegan:Connect(function(i, p) if not p and i.KeyCode == Enum.KeyCode.RightControl then toggleUI(not MainFrame.Visible) end end)

local Container = cre("Frame", MainFrame, {Size = UDim2.new(1, -20, 1, -95), Position = UDim2.new(0, 10, 0, 85), BackgroundTransparency = 1, ZIndex = 2})
local FarmPage = cre("ScrollingFrame", Container, {Size = UDim2.new(1, 0, 1, 0), BackgroundTransparency = 1, CanvasSize = UDim2.new(0, 0, 0, 400), ScrollBarThickness = 3, ZIndex = 2})
cre("UIListLayout", FarmPage, {FillDirection = Enum.FillDirection.Vertical, Padding = UDim.new(0, 8)})

-- ==========================================
-- VÒNG LẶP KHÓA TỐC ĐỘ
-- ==========================================
RunService.Heartbeat:Connect(function()
    pcall(function()
        if _G.EnableSpeed then
            local char = Player.Character
            local hum = char and char:FindFirstChildOfClass("Humanoid")
            if hum and hum.WalkSpeed ~= _G.WalkSpeed then
                hum.WalkSpeed = _G.WalkSpeed
            end
        end
    end)
end)

-- Slider Speed
local SF = cre("Frame", FarmPage, {Size = UDim2.new(1, -5, 0, 45), BackgroundColor3 = Color3.fromRGB(30, 30, 38), ZIndex = 2})
cre("UICorner", SF, {CornerRadius = UDim.new(0, 6)})
cre("TextLabel", SF, {Size = UDim2.new(0.6, 0, 0, 20), Position = UDim2.new(0, 10, 0, 3), BackgroundTransparency = 1, Text = "Tốc độ di chuyển", TextColor3 = Color3.fromRGB(220, 220, 220), Font = Enum.Font.Gotham, TextSize = 12, ZIndex = 2})
local SV = cre("TextLabel", SF, {Size = UDim2.new(0.3, 0, 0, 20), Position = UDim2.new(0.7, -10, 0, 3), BackgroundTransparency = 1, Text = tostring(_G.WalkSpeed), TextColor3 = Color3.fromRGB(74, 77, 240), Font = Enum.Font.GothamBold, TextSize = 12, TextXAlignment = Enum.TextXAlignment.Right, ZIndex = 2})
local SB = cre("TextButton", SF, {Size = UDim2.new(1, -20, 0, 6), Position = UDim2.new(0, 10, 0, 28), BackgroundColor3 = Color3.fromRGB(50, 50, 60), Text = "", AutoButtonColor = false, ZIndex = 2})
cre("UICorner", SB, {CornerRadius = UDim.new(0, 3)})
local SFill = cre("Frame", SB, {Size = UDim2.new(math.clamp((_G.WalkSpeed - 16) / 284, 0, 1), 0, 1, 0), BackgroundColor3 = Color3.fromRGB(74, 77, 240), BorderSizePixel = 0, ZIndex = 2})
cre("UICorner", SFill, {CornerRadius = UDim.new(0, 3)})

local sliding = false
local function updSlider(i)
    local pct = math.clamp((i.Position.X - SB.AbsolutePosition.X) / SB.AbsoluteSize.X, 0, 1)
    SFill.Size = UDim2.new(pct, 0, 1, 0)
    _G.WalkSpeed = math.floor(16 + 284 * pct)
    SV.Text = tostring(_G.WalkSpeed)
end
SB.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then sliding = true updSlider(i) end end)
UIS.InputEnded:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then if sliding then sliding = false saveConfig() end end end)
UIS.InputChanged:Connect(function(i) if sliding and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then updSlider(i) end end)

-- Nút Bật/Tắt
local function addToggle(text, var)
    local TF = cre("Frame", FarmPage, {Size = UDim2.new(1, -5, 0, 40), BackgroundColor3 = Color3.fromRGB(30, 30, 38), ZIndex = 2})
    cre("UICorner", TF, {CornerRadius = UDim.new(0, 6)})
    cre("TextLabel", TF, {Size = UDim2.new(0.7, 0, 1, 0), Position = UDim2.new(0, 10, 0, 0), BackgroundTransparency = 1, Text = text, TextColor3 = Color3.fromRGB(220, 220, 220), Font = Enum.Font.Gotham, TextSize = 13, ZIndex = 2})
    local TB = cre("TextButton", TF, {Size = UDim2.new(0, 34, 0, 18), Position = UDim2.new(1, -44, 0.5, -9), BackgroundColor3 = _G[var] and Color3.fromRGB(74, 77, 240) or Color3.fromRGB(50, 50, 60), Text = "", AutoButtonColor = false, ZIndex = 2})
    cre("UICorner", TB, {CornerRadius = UDim.new(1, 0)})
    local C = cre("Frame", TB, {Size = UDim2.new(0, 14, 0, 14), Position = _G[var] and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7), BackgroundColor3 = Color3.fromRGB(255, 255, 255), ZIndex = 2})
    cre("UICorner", C, {CornerRadius = UDim.new(1, 0)})
    TB.MouseButton1Click:Connect(function()
        _G[var] = not _G[var]
        TB.BackgroundColor3 = _G[var] and Color3.fromRGB(74, 77, 240) or Color3.fromRGB(50, 50, 60)
        C.Position = _G[var] and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7)
        saveConfig()
    end)
end

addToggle("Bật/Tắt Tốc độ (Enable Speed)", "EnableSpeed")
addToggle("Nhảy vô hạn (Infinite Jump)", "InfJump")
UIS.JumpRequest:Connect(function() if _G.InfJump then pcall(function() Player.Character.Humanoid:ChangeState("Jumping") end) end end)

-- Anti-AFK
Player.Idled:Connect(function() VU:CaptureController(); VU:ClickButton2(Vector2.new(0,0)) end)
