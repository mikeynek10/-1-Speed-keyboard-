local Players, UIS, CoreGui, VU, RunService, HttpService, Workspace = game:GetService("Players"), game:GetService("UserInputService"), game:GetService("CoreGui"), game:GetService("VirtualUser"), game:GetService("RunService"), game:GetService("HttpService"), game:GetService("Workspace")
local Player = Players.LocalPlayer
local ParentGui = pcall(function() return CoreGui.Name end) and CoreGui or Player:WaitForChild("PlayerGui")

if ParentGui:FindFirstChild("MikeySpeedHub") then ParentGui["MikeySpeedHub"]:Destroy() end

local toggleUpdates = {}

-- ==========================================
-- CẤU HÌNH
-- ==========================================
local ConfigFile = "MikeySpeedHub_Config.json"
_G.WalkSpeed, _G.FlySpeed, _G.InfJump, _G.EnableSpeed, _G.Flying, _G.FlyKey = 16, 10, false, false, false, "F"

local function saveConfig()
    local data = {WalkSpeed = _G.WalkSpeed, FlySpeed = _G.FlySpeed, InfJump = _G.InfJump, EnableSpeed = _G.EnableSpeed, Flying = _G.Flying, FlyKey = _G.FlyKey}
    if writefile then pcall(function() writefile(ConfigFile, HttpService:JSONEncode(data)) end) end
end

local function loadConfig()
    if readfile and isfile and isfile(ConfigFile) then
        local success, data = pcall(function() return HttpService:JSONDecode(readfile(ConfigFile)) end)
        if success and data then
            _G.WalkSpeed = math.clamp(data.WalkSpeed or 16, 16, 300)
            _G.FlySpeed = math.clamp(data.FlySpeed or 10, 5, 25) 
            _G.InfJump = data.InfJump or false
            _G.EnableSpeed = data.EnableSpeed or false
            _G.Flying = data.Flying or false
            _G.FlyKey = data.FlyKey or "F"
        end
    end
end
loadConfig()

-- ==========================================
-- GIAO DIỆN (UI) ĐỒNG BỘ
-- ==========================================
local function cre(cls, parent, props)
    local inst = Instance.new(cls)
    if props then for k,v in pairs(props) do inst[k] = v end end
    if parent then inst.Parent = parent end
    return inst
end

local ScreenGui = cre("ScreenGui", ParentGui, {Name = "MikeySpeedHub", ResetOnSpawn = false})

-- Nút Menu (Size 120x30)
local TopBar = cre("TextButton", ScreenGui, {Name = "TopBarToggle", Size = UDim2.new(0, 120, 0, 30), Position = UDim2.new(0, 10, 0, 60), BackgroundColor3 = Color3.fromRGB(20, 20, 25), Text = "⚡ Menu", TextColor3 = Color3.fromRGB(255, 255, 255), Font = Enum.Font.GothamBold, TextSize = 12, AutoButtonColor = false, Visible = false})
cre("UICorner", TopBar, {CornerRadius = UDim.new(0, 6)})
cre("UIStroke", TopBar, {Color = Color3.fromRGB(74, 77, 240), Thickness = 1.5})

-- Nút Fly (Size 120x30)
local QuickFlyBtn = cre("TextButton", ScreenGui, {Name = "QuickFlyBtn", Size = UDim2.new(0, 120, 0, 30), Position = UDim2.new(0, 10, 0, 100), BackgroundColor3 = Color3.fromRGB(35, 35, 45), Text = "✈️ Fly: TẮT", TextColor3 = Color3.fromRGB(255, 255, 255), Font = Enum.Font.GothamBold, TextSize = 12, AutoButtonColor = true, Visible = false})
cre("UICorner", QuickFlyBtn, {CornerRadius = UDim.new(0, 6)})
local QuickFlyStroke = cre("UIStroke", QuickFlyBtn, {Color = Color3.fromRGB(74, 77, 240), Thickness = 1.5})

-- Main Frame
local MainFrame = cre("Frame", ScreenGui, {Name = "MainFrame", Size = UDim2.new(0, 460, 0, 350), Position = UDim2.new(0.5, -230, 0.5, -175), BackgroundColor3 = Color3.fromRGB(25, 25, 30), Active = true, Draggable = true, Visible = true})
cre("UICorner", MainFrame, {CornerRadius = UDim.new(0, 10)})
cre("UIStroke", MainFrame, {Color = Color3.fromRGB(74, 77, 240), Thickness = 1.8})

local function toggleUI(v) MainFrame.Visible = v; TopBar.Visible = not v; QuickFlyBtn.Visible = not v end
TopBar.MouseButton1Click:Connect(function() toggleUI(true) end)

local Header = cre("Frame", MainFrame, {Size = UDim2.new(1, 0, 0, 38), BackgroundTransparency = 1})
cre("TextLabel", Header, {Size = UDim2.new(0.8, 0, 1, 0), Position = UDim2.new(0, 15, 0, 0), BackgroundTransparency = 1, Text = "+1 speed keyboard ( MIKEY )", TextColor3 = Color3.fromRGB(255, 255, 255), Font = Enum.Font.GothamBold, TextSize = 14, TextXAlignment = Enum.TextXAlignment.Left})
local CloseBtn = cre("TextButton", Header, {Size = UDim2.new(0, 26, 0, 26), Position = UDim2.new(1, -35, 0.5, -13), BackgroundColor3 = Color3.fromRGB(35, 35, 45), Text = "✕", TextColor3 = Color3.fromRGB(230, 230, 230), Font = Enum.Font.GothamBold, TextSize = 12, AutoButtonColor = false})
cre("UICorner", CloseBtn, {CornerRadius = UDim.new(0, 6)})
CloseBtn.MouseButton1Click:Connect(function() toggleUI(false) end)
UIS.InputBegan:Connect(function(i, p) if not p and i.KeyCode == Enum.KeyCode.RightControl then toggleUI(not MainFrame.Visible) end end)

-- Keybind & Visual Logic
UIS.InputBegan:Connect(function(i, p) 
    if not p then 
        local success, key = pcall(function() return Enum.KeyCode[_G.FlyKey] end)
        if success and i.KeyCode == key then _G.Flying = not _G.Flying; if toggleUpdates["Flying"] then toggleUpdates["Flying"]() end; updateQuickFlyVisual(); saveConfig() end 
    end 
end)

function updateQuickFlyVisual()
    if _G.Flying then QuickFlyBtn.BackgroundColor3 = Color3.fromRGB(74, 77, 240); QuickFlyBtn.Text = "✈️ Fly: BẬT"; QuickFlyStroke.Color = Color3.fromRGB(255, 255, 255)
    else QuickFlyBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 45); QuickFlyBtn.Text = "✈️ Fly: TẮT"; QuickFlyStroke.Color = Color3.fromRGB(74, 77, 240) end
end
QuickFlyBtn.MouseButton1Click:Connect(function() _G.Flying = not _G.Flying; if toggleUpdates["Flying"] then toggleUpdates["Flying"]() end; updateQuickFlyVisual(); saveConfig() end)
updateQuickFlyVisual()

-- Tabs & Content
local Container = cre("Frame", MainFrame, {Size = UDim2.new(1, -20, 1, -90), Position = UDim2.new(0, 10, 0, 80), BackgroundTransparency = 1})
local MainPage = cre("ScrollingFrame", Container, {Size = UDim2.new(1, 0, 1, 0), BackgroundTransparency = 1, CanvasSize = UDim2.new(0, 0, 0, 300), ScrollBarThickness = 3, Visible = true})
local FlyPage = cre("ScrollingFrame", Container, {Size = UDim2.new(1, 0, 1, 0), BackgroundTransparency = 1, CanvasSize = UDim2.new(0, 0, 0, 300), ScrollBarThickness = 3, Visible = false})
cre("UIListLayout", MainPage, {Padding = UDim.new(0, 8)})
cre("UIListLayout", FlyPage, {Padding = UDim.new(0, 8)})

local TabBar = cre("Frame", MainFrame, {Size = UDim2.new(1, -20, 0, 30), Position = UDim2.new(0, 10, 0, 42), BackgroundTransparency = 1})
cre("UIListLayout", TabBar, {FillDirection = Enum.FillDirection.Horizontal, Padding = UDim.new(0, 8)})
local MainTabBtn = cre("TextButton", TabBar, {Size = UDim2.new(0, 110, 1, 0), BackgroundColor3 = Color3.fromRGB(74, 77, 240), Text = "🏠 Tab Chính", TextColor3 = Color3.fromRGB(255, 255, 255), Font = Enum.Font.GothamBold, TextSize = 12, AutoButtonColor = false})
local FlyTabBtn = cre("TextButton", TabBar, {Size = UDim2.new(0, 110, 1, 0), BackgroundColor3 = Color3.fromRGB(35, 35, 45), Text = "✈️ Tab Fly", TextColor3 = Color3.fromRGB(200, 200, 200), Font = Enum.Font.GothamBold, TextSize = 12, AutoButtonColor = false})
MainTabBtn.MouseButton1Click:Connect(function() MainPage.Visible = true; FlyPage.Visible = false; MainTabBtn.BackgroundColor3 = Color3.fromRGB(74, 77, 240); FlyTabBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 45) end)
FlyTabBtn.MouseButton1Click:Connect(function() MainPage.Visible = false; FlyPage.Visible = true; MainTabBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 45); FlyTabBtn.BackgroundColor3 = Color3.fromRGB(74, 77, 240) end)

-- Core Loop
local bv, bg = nil, nil
RunService.Heartbeat:Connect(function()
    pcall(function()
        local char = Player.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if _G.EnableSpeed and hum and hum.WalkSpeed ~= _G.WalkSpeed then hum.WalkSpeed = math.clamp(_G.WalkSpeed, 16, 300) end
        if _G.Flying and root and hum then
            hum.PlatformStand = true
            local cam = Workspace.CurrentCamera
            if not bv or bv.Parent ~= root then if bv then bv:Destroy() end bv = Instance.new("BodyVelocity") bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge) bv.Parent = root end
            if not bg or bg.Parent ~= root then if bg then bg:Destroy() end bg = Instance.new("BodyGyro") bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge) bg.Parent = root end
            bg.CFrame = CFrame.lookAt(root.Position, root.Position + Vector3.new(cam.CFrame.LookVector.X, 0, cam.CFrame.LookVector.Z))
            local moveDir = Vector3.new(0, 0, 0)
            if UIS:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + cam.CFrame.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - cam.CFrame.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - cam.CFrame.RightVector end
            if UIS:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + cam.CFrame.RightVector end
            if moveDir.Magnitude == 0 and hum.MoveDirection.Magnitude > 0 then
                local camLookVec = cam.CFrame.LookVector
                local camRightVec = cam.CFrame.RightVector
                local camHorizontalLook = Vector3.new(camLookVec.X, 0, camLookVec.Z).Unit
                local forwardDot = hum.MoveDirection:Dot(camHorizontalLook)
                local rightDot = hum.MoveDirection:Dot(camRightVec)
                moveDir = (camLookVec * forwardDot) + (camRightVec * rightDot)
            end
            if moveDir.Magnitude > 0 then bv.Velocity = moveDir.Unit * (_G.FlySpeed * 8) else bv.Velocity = Vector3.new(0, 0, 0) end
        else
            if bv then bv:Destroy() bv = nil end
            if bg then bg:Destroy() bg = nil end
            if hum and hum.PlatformStand then hum.PlatformStand = false end
        end
    end)
end)

-- Helpers
local function addInput(p, t, v, mi, ma) local TF = cre("Frame", p, {Size = UDim2.new(1, -5, 0, 45), BackgroundColor3 = Color3.fromRGB(30, 30, 38)}); cre("UICorner", TF, {CornerRadius = UDim.new(0, 6)}); cre("TextLabel", TF, {Size = UDim2.new(0, 250, 1, 0), Position = UDim2.new(0, 10, 0, 0), BackgroundTransparency = 1, Text = t, TextColor3 = Color3.fromRGB(220, 220, 220), Font = Enum.Font.Gotham, TextSize = 13, TextXAlignment = Enum.TextXAlignment.Left}); local TB = cre("TextBox", TF, {Size = UDim2.new(0, 75, 0, 26), Position = UDim2.new(1, -85, 0.5, -13), BackgroundColor3 = Color3.fromRGB(20, 20, 25), Text = tostring(_G[v]), TextColor3 = Color3.fromRGB(255, 255, 255), Font = Enum.Font.GothamBold, TextSize = 12}); cre("UICorner", TB, {CornerRadius = UDim.new(0, 6)}); TB.FocusLost:Connect(function() local n = tonumber(TB.Text) if n then _G[v] = math.clamp(math.floor(n), mi, ma); TB.Text = tostring(_G[v]); saveConfig() else TB.Text = tostring(_G[v]) end end) end
local function addToggle(p, t, v) local TF = cre("Frame", p, {Size = UDim2.new(1, -5, 0, 40), BackgroundColor3 = Color3.fromRGB(30, 30, 38)}); cre("UICorner", TF, {CornerRadius = UDim.new(0, 6)}); cre("TextLabel", TF, {Size = UDim2.new(0.7, 0, 1, 0), Position = UDim2.new(0, 10, 0, 0), BackgroundTransparency = 1, Text = t, TextColor3 = Color3.fromRGB(220, 220, 220), Font = Enum.Font.Gotham, TextSize = 13}); local TB = cre("TextButton", TF, {Size = UDim2.new(0, 34, 0, 18), Position = UDim2.new(1, -44, 0.5, -9), BackgroundColor3 = _G[v] and Color3.fromRGB(74, 77, 240) or Color3.fromRGB(50, 50, 60), Text = "", AutoButtonColor = false}); cre("UICorner", TB, {CornerRadius = UDim.new(1, 0)}); local C = cre("Frame", TB, {Size = UDim2.new(0, 14, 0, 14), Position = _G[v] and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7), BackgroundColor3 = Color3.fromRGB(255, 255, 255)}); cre("UICorner", C, {CornerRadius = UDim.new(1, 0)}); local function update() TB.BackgroundColor3 = _G[v] and Color3.fromRGB(74, 77, 240) or Color3.fromRGB(50, 50, 60); C.Position = _G[v] and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7) end; toggleUpdates[v] = update; TB.MouseButton1Click:Connect(function() _G[v] = not _G[v]; update(); if v == "Flying" then updateQuickFlyVisual() end; saveConfig() end) end
local function addKeybind(p, t, v) local TF = cre("Frame", p, {Size = UDim2.new(1, -5, 0, 45), BackgroundColor3 = Color3.fromRGB(30, 30, 38)}); cre("UICorner", TF, {CornerRadius = UDim.new(0, 6)}); cre("TextLabel", TF, {Size = UDim2.new(0, 250, 1, 0), Position = UDim2.new(0, 10, 0, 0), BackgroundTransparency = 1, Text = t, TextColor3 = Color3.fromRGB(220, 220, 220), Font = Enum.Font.Gotham, TextSize = 13, TextXAlignment = Enum.TextXAlignment.Left}); local TB = cre("TextButton", TF, {Size = UDim2.new(0, 95, 0, 26), Position = UDim2.new(1, -105, 0.5, -13), BackgroundColor3 = Color3.fromRGB(20, 20, 25), Text = _G[v], TextColor3 = Color3.fromRGB(255, 255, 255), Font = Enum.Font.GothamBold, TextSize = 11}); cre("UICorner", TB, {CornerRadius = UDim.new(0, 6)}); TB.MouseButton1Click:Connect(function() TB.Text = "..."; local conn; conn = UIS.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.Keyboard then _G[v] = i.KeyCode.Name; TB.Text = i.KeyCode.Name; saveConfig(); conn:Disconnect() end end) end) end -- Đã thêm end đóng hàm ở đây

addInput(MainPage, "Nhập tốc độ chạy (16-300)", "WalkSpeed", 16, 300)
addToggle(MainPage, "Bật Tốc độ", "EnableSpeed")
addToggle(MainPage, "Nhảy vô hạn", "InfJump")
addInput(FlyPage, "Tốc độ bay (5-25)", "FlySpeed", 5, 25)
addKeybind(FlyPage, "Phím bật Fly", "FlyKey")
UIS.JumpRequest:Connect(function() if _G.InfJump then pcall(function() Player.Character.Humanoid:ChangeState("Jumping") end) end end)
