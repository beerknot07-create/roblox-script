local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

if LocalPlayer.PlayerGui:FindFirstChild("ZxtataGui") then
    LocalPlayer.PlayerGui.ZxtataGui:Destroy()
end

local flying, espEnabled, speedEnabled, jumpEnabled, invisEnabled, noclipEnabled, hitboxEnabled = false, false, false, false, false, false, false
local following, pulling = false, false
local targetName = ""
local speedVal, jumpVal = 50, 100
local flySpeed = 50
local hitboxSize = 5
local currentCategory = "Main"
local savedParts = {}

local ScreenGui = Instance.new("ScreenGui", LocalPlayer.PlayerGui)
ScreenGui.Name = "ZxtataGui"
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 280, 0, 480)
MainFrame.Position = UDim2.new(0.5, -140, 0.3, -240)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.BorderSizePixel = 0

local FrameCorner = Instance.new("UICorner", MainFrame)
FrameCorner.CornerRadius = UDim.new(0, 8)

local TitleLabel = Instance.new("TextLabel", MainFrame)
TitleLabel.Size = UDim2.new(1, -30, 0, 35)
TitleLabel.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
TitleLabel.Text = "  zxtata | Fly V3 & Hitbox"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.Font = Enum.Font.SourceSansBold
TitleLabel.TextSize = 14
TitleLabel.BorderSizePixel = 0

local TitleCorner = Instance.new("UICorner", TitleLabel)
TitleCorner.CornerRadius = UDim.new(0, 8)

local MinimizeBtn = Instance.new("TextButton", MainFrame)
MinimizeBtn.Size = UDim2.new(0, 30, 0, 35)
MinimizeBtn.Position = UDim2.new(1, -30, 0, 0)
MinimizeBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
MinimizeBtn.Text = "-"
MinimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeBtn.BorderSizePixel = 0

local MinCorner = Instance.new("UICorner", MinimizeBtn)
MinCorner.CornerRadius = UDim.new(0, 8)

local TabMainBtn = Instance.new("TextButton", MainFrame)
TabMainBtn.Size = UDim2.new(0.5, 0, 0, 30)
TabMainBtn.Position = UDim2.new(0, 0, 0, 35)
TabMainBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
TabMainBtn.Text = "หมวดหมู่หลัก"
TabMainBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
TabMainBtn.Font = Enum.Font.SourceSansBold
TabMainBtn.TextSize = 13
TabMainBtn.BorderSizePixel = 0

local TabTrollBtn = Instance.new("TextButton", MainFrame)
TabTrollBtn.Size = UDim2.new(0.5, 0, 0, 30)
TabTrollBtn.Position = UDim2.new(0.5, 0, 0, 35)
TabTrollBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TabTrollBtn.Text = "หมวดหมู่ป่วน"
TabTrollBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
TabTrollBtn.Font = Enum.Font.SourceSansBold
TabTrollBtn.TextSize = 13
TabTrollBtn.BorderSizePixel = 0

local MainContainer = Instance.new("Frame", MainFrame)
MainContainer.Size = UDim2.new(1, 0, 1, -65)
MainContainer.Position = UDim2.new(0, 0, 0, 65)
MainContainer.BackgroundTransparency = 1

local TrollContainer = Instance.new("Frame", MainFrame)
TrollContainer.Size = UDim2.new(1, 0, 1, -65)
TrollContainer.Position = UDim2.new(0, 0, 0, 65)
TrollContainer.BackgroundTransparency = 1
TrollContainer.Visible = false

local function createButton(parent, text, yPos)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(0, 240, 0, 35)
    btn.Position = UDim2.new(0, 20, 0, yPos)
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 100, 100)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 14
    btn.BorderSizePixel = 0
    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0, 6)
    return btn
end

local FlyBtn = createButton(MainContainer, "ระบบ บิน Fly V3: ปิดอยู่", 10)

local FlySpeedBox = Instance.new("TextBox", MainContainer)
FlySpeedBox.Size = UDim2.new(0, 240, 0, 30)
FlySpeedBox.Position = UDim2.new(0, 20, 0, 55)
FlySpeedBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
FlySpeedBox.PlaceholderText = "⚡ ความเร็วบิน (เริ่มต้น 50)..."
FlySpeedBox.Text = "50"
FlySpeedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
FlySpeedBox.Font = Enum.Font.SourceSans
FlySpeedBox.TextSize = 13
FlySpeedBox.BorderSizePixel = 0
local SpeedCorner = Instance.new("UICorner", FlySpeedBox)
SpeedCorner.CornerRadius = UDim.new(0, 6)

FlySpeedBox.FocusLost:Connect(function()
    local num = tonumber(FlySpeedBox.Text)
    if num then flySpeed = num else flySpeed = 50 FlySpeedBox.Text = "50" end
end)

local HitboxBtn = createButton(MainContainer, "ระบบ ขยาย Hitbox (หัวโต): ปิดอยู่", 95)

local HitboxSizeBox = Instance.new("TextBox", MainContainer)
HitboxSizeBox.Size = UDim2.new(0, 240, 0, 30)
HitboxSizeBox.Position = UDim2.new(0, 20, 0, 140)
HitboxSizeBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
HitboxSizeBox.PlaceholderText = "🎯 ขนาด Hitbox (เริ่มต้น 5)..."
HitboxSizeBox.Text = "5"
HitboxSizeBox.TextColor3 = Color3.fromRGB(255, 255, 255)
HitboxSizeBox.Font = Enum.Font.SourceSans
HitboxSizeBox.TextSize = 13
HitboxSizeBox.BorderSizePixel = 0
local HitboxCorner = Instance.new("UICorner", HitboxSizeBox)
HitboxCorner.CornerRadius = UDim.new(0, 6)

HitboxSizeBox.FocusLost:Connect(function()
    local num = tonumber(HitboxSizeBox.Text)
    if num then hitboxSize = num else hitboxSize = 5 HitboxSizeBox.Text = "5" end
end)

local EspBtn = createButton(MainContainer, "ระบบ มองทะลุ (ESP): ปิดอยู่", 180)
local SpeedBtn = createButton(MainContainer, "ระบบ วิ่งเร็ว: ปิดอยู่", 225)
local JumpBtn = createButton(MainContainer, "ระบบ กระโดดสูง: ปิดอยู่", 270)
local NoclipBtn = createButton(MainContainer, "ระบบ ทะลุกำแพง (Noclip): ปิดอยู่", 315)
local InvisBtn = createButton(MainContainer, "ระบบ ล่องหน: ปิดอยู่", 360)

local FollowBtn = createButton(TrollContainer, "ระบบ วาร์ปตามติดตัว: ปิดอยู่", 15)

local NameBox = Instance.new("TextBox", TrollContainer)
NameBox.Size = UDim2.new(0, 240, 0, 30)
NameBox.Position = UDim2.new(0, 20, 0, 65)
NameBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
NameBox.PlaceholderText = "👉 พิมพ์ชื่อคนที่จะตามตรงนี้..."
NameBox.Text = ""
NameBox.TextColor3 = Color3.fromRGB(255, 255, 255)
NameBox.Font = Enum.Font.SourceSans
NameBox.TextSize = 13
NameBox.BorderSizePixel = 0
local NameCorner = Instance.new("UICorner", NameBox)
NameCorner.CornerRadius = UDim.new(0, 6)

NameBox.FocusLost:Connect(function()
    targetName = NameBox.Text
end)

local PullBtn = createButton(TrollContainer, "ระบบ ดึงคนทั้งเซิร์ฟ: ปิดอยู่", 120)

TabMainBtn.MouseButton1Click:Connect(function()
    currentCategory = "Main"
    MainContainer.Visible = true
    TrollContainer.Visible = false
    TabMainBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
    TabMainBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
    TabTrollBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    TabTrollBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
    TitleLabel.Text = "  zxtata | Fly V3 & Hitbox"
end)

TabTrollBtn.MouseButton1Click:Connect(function()
    currentCategory = "Troll"
    MainContainer.Visible = false
    TrollContainer.Visible = true
    TabTrollBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
    TabTrollBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
    TabMainBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    TabMainBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
    TitleLabel.Text = "  zxtata | หมวดหมู่ป่วน"
end)

local isMinimized = false
MinimizeBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    MainContainer.Visible = not isMinimized and (currentCategory == "Main")
    TrollContainer.Visible = not isMinimized and (currentCategory == "Troll")
    TabMainBtn.Visible = not isMinimized
    TabTrollBtn.Visible = not isMinimized
    MainFrame.Size = isMinimized and UDim2.new(0, 280, 0, 35) or UDim2.new(0, 280, 0, 480)
    MinimizeBtn.Text = isMinimized and "+" or "-"
end)

FlyBtn.MouseButton1Click:Connect(function()
    flying = not flying
    FlyBtn.Text = flying and "ระบบ บิน Fly V3: เปิดใช้งาน" or "ระบบ บิน Fly V3: ปิดอยู่"
    FlyBtn.TextColor3 = flying and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    
    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChild("Humanoid")
    if hum then
        hum.PlatformStand = flying
    end
end)

HitboxBtn.MouseButton1Click:Connect(function()
    hitboxEnabled = not hitboxEnabled
    HitboxBtn.Text = hitboxEnabled and "ระบบ ขยาย Hitbox (หัวโต): เปิดใช้งาน" or "ระบบ ขยาย Hitbox (หัวโต): ปิดอยู่"
    HitboxBtn.TextColor3 = hitboxEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    if not hitboxEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
                p.Character.Head.Size = Vector3.new(2, 1, 1)
                p.Character.Head.Transparency = 0
                p.Character.Head.CanCollide = true
            end
        end
    end
end)

EspBtn.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    EspBtn.Text = espEnabled and "ระบบ มองทะลุ (ESP): เปิดใช้งาน" or "ระบบ มองทะลุ (ESP): ปิดอยู่"
    EspBtn.TextColor3 = espEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    if not espEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p.Character and p.Character:FindFirstChild("Highlight") then
                p.Character.Highlight:Destroy()
            end
        end
    end
end)

SpeedBtn.MouseButton1Click:Connect(function()
    speedEnabled = not speedEnabled
    SpeedBtn.Text = speedEnabled and "ระบบ วิ่งเร็ว: เปิดใช้งาน" or "ระบบ วิ่งเร็ว: ปิดอยู่"
    SpeedBtn.TextColor3 = speedEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

JumpBtn.MouseButton1Click:Connect(function()
    jumpEnabled = not jumpEnabled
    JumpBtn.Text = jumpEnabled and "ระบบ กระโดดสูง: เปิดใช้งาน" or "ระบบ กระโดดสูง: ปิดอยู่"
    JumpBtn.TextColor3 = jumpEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

NoclipBtn.MouseButton1Click:Connect(function()
    noclipEnabled = not noclipEnabled
    NoclipBtn.Text = noclipEnabled and "ระบบ ทะลุกำแพง (Noclip): เปิดใช้งาน" or "ระบบ ทะลุกำแพง (Noclip): ปิดอยู่"
    NoclipBtn.TextColor3 = noclipEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

InvisBtn.MouseButton1Click:Connect(function()
    invisEnabled = not invisEnabled
    InvisBtn.Text = invisEnabled and "ระบบ ล่องหน: เปิดใช้งาน" or "ระบบ ล่องหน: ปิดอยู่"
    InvisBtn.TextColor3 = invisEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    
    local char = LocalPlayer.Character
    if char then
        if invisEnabled then
            for _, v in ipairs(char:GetDescendants()) do
                if v:IsA("BasePart") or v:IsA("Decal") then
                    savedParts[v] = v.Transparency
                    v.Transparency = 1
                end
            end
        else
            for part, originalTran in pairs(savedParts) do
                if part and part.Parent then part.Transparency = originalTran end
            end
            savedParts = {}
        end
    end
end)

FollowBtn.MouseButton1Click:Connect(function()
    following = not following
    FollowBtn.Text = following and "ระบบ วาร์ปตามติดตัว: เปิดใช้งาน" or "ระบบ วาร์ปตามติดตัว: ปิดอยู่"
    FollowBtn.TextColor3 = following and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

PullBtn.MouseButton1Click:Connect(function()
    pulling = not pulling
    PullBtn.Text = pulling and "ระบบ ดึงคนทั้งเซิร์ฟ: เปิดใช้งาน" or "ระบบ ดึงคนทั้งเซิร์ฟ: ปิดอยู่"
    PullBtn.TextColor3 = pulling and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

RunService.Heartbeat:Connect(function(dt)
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") or not char:FindFirstChild("Humanoid") then return end
    local hrp = char.HumanoidRootPart
    local hum = char.Humanoid

    if speedEnabled then hum.WalkSpeed = speedVal else hum.WalkSpeed = 16 end
    if jumpEnabled then hum.JumpPower = jumpVal else hum.JumpPower = 50 end

    if noclipEnabled then
        for _, part in ipairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end

    if hitboxEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
                local head = p.Character.Head
                head.Size = Vector3.new(hitboxSize, hitboxSize, hitboxSize)
                head.Transparency = 0.5
                head.CanCollide = false
            end
        end
    end

    -- ระบบ Fly V3 (ลอยตัวและพุ่งตามทิศทางกล้องแบบสคริปต์ Fly V3 แท้ๆ ลื่นไหลสมบูรณ์)
    if flying then
        hum.PlatformStand = true
        local camCFrame = Camera.CFrame
        local moveDir = hum.MoveDirection
        
        local flyVector = Vector3.new(0, 0, 0)
        if moveDir.Magnitude > 0 then
            flyVector = (camCFrame.RightVector * moveDir.X + camCFrame.LookVector * -moveDir.Z).Unit
        else
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then flyVector = flyVector + camCFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then flyVector = flyVector - camCFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then flyVector = flyVector + camCFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then flyVector = flyVector - camCFrame.RightVector end
        end

        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
            flyVector = flyVector + Vector3.new(0, 1, 0)
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) or UserInputService:IsKeyDown(Enum.KeyCode.RightShift) then
            flyVector = flyVector - Vector3.new(0, 1, 0)
        end

        if flyVector.Magnitude > 0 then
            hrp.Velocity = flyVector.Unit * flySpeed
        else
            hrp.Velocity = Vector3.new(0, 0, 0)
        end
        hrp.CFrame = CFrame.new(hrp.Position, hrp.Position + camCFrame.LookVector)
    end

    if espEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                if not p.Character:FindFirstChild("Highlight") then
                    local hl = Instance.new("Highlight", p.Character)
                    hl.FillColor = Color3.fromRGB(255, 0, 0)
                    hl.OutlineColor = Color3.fromRGB(255, 255, 255)
                end
            end
        end
    end

    if following and targetName ~= "" then
        local targetP = nil
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and (p.Name:lower():sub(1, #targetName) == targetName:lower() or p.DisplayName:lower():sub(1, #targetName) == targetName:lower()) then
                targetP = p
                break
            end
        end
        if targetP and targetP.Character and targetP.Character:FindFirstChild("HumanoidRootPart") then
            local targetHrp = targetP.Character.HumanoidRootPart
            hrp.CFrame = targetHrp.CFrame * CFrame.new(0, 0, 3)
        end
    end

    if pulling then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local targetHrp = p.Character.HumanoidRootPart
                targetHrp.CFrame = hrp.CFrame * CFrame.new(0, 0, -3)
                targetHrp.Velocity = Vector3.new(0, 0, 0)
            end
        end
    end
end)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

-- ป้องกันหน้าต่างซ้อน
if LocalPlayer.PlayerGui:FindFirstChild("ZxtataGui") then
    LocalPlayer.PlayerGui.ZxtataGui:Destroy()
end

-- สถานะระบบ
local flying, espEnabled, speedEnabled, jumpEnabled, invisEnabled, noclipEnabled, hitboxEnabled = false, false, false, false, false, false, false
local following, pulling = false, false
local targetName = ""
local speedVal, jumpVal = 50, 100
local flySpeed = 50
local hitboxSize = 5
local currentCategory = "Main"
local savedParts = {}
local flyPlatform = nil

-- สร้างหน้าต่าง UI หลัก
local ScreenGui = Instance.new("ScreenGui", LocalPlayer.PlayerGui)
ScreenGui.Name = "ZxtataGui"
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 280, 0, 480)
MainFrame.Position = UDim2.new(0.5, -140, 0.3, -240)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.BorderSizePixel = 0

local FrameCorner = Instance.new("UICorner", MainFrame)
FrameCorner.CornerRadius = UDim.new(0, 8)

local TitleLabel = Instance.new("TextLabel", MainFrame)
TitleLabel.Size = UDim2.new(1, -30, 0, 35)
TitleLabel.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
TitleLabel.Text = "  zxtata | Fly & Hitbox Fix"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.Font = Enum.Font.SourceSansBold
TitleLabel.TextSize = 14
TitleLabel.BorderSizePixel = 0

local TitleCorner = Instance.new("UICorner", TitleLabel)
TitleCorner.CornerRadius = UDim.new(0, 8)

local MinimizeBtn = Instance.new("TextButton", MainFrame)
MinimizeBtn.Size = UDim2.new(0, 30, 0, 35)
MinimizeBtn.Position = UDim2.new(1, -30, 0, 0)
MinimizeBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
MinimizeBtn.Text = "-"
MinimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeBtn.BorderSizePixel = 0

local MinCorner = Instance.new("UICorner", MinimizeBtn)
MinCorner.CornerRadius = UDim.new(0, 8)

-- ปุ่มสลับหมวดหมู่
local TabMainBtn = Instance.new("TextButton", MainFrame)
TabMainBtn.Size = UDim2.new(0.5, 0, 0, 30)
TabMainBtn.Position = UDim2.new(0, 0, 0, 35)
TabMainBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
TabMainBtn.Text = "หมวดหมู่หลัก"
TabMainBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
TabMainBtn.Font = Enum.Font.SourceSansBold
TabMainBtn.TextSize = 13
TabMainBtn.BorderSizePixel = 0

local TabTrollBtn = Instance.new("TextButton", MainFrame)
TabTrollBtn.Size = UDim2.new(0.5, 0, 0, 30)
TabTrollBtn.Position = UDim2.new(0.5, 0, 0, 35)
TabTrollBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TabTrollBtn.Text = "หมวดหมู่ป่วน"
TabTrollBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
TabTrollBtn.Font = Enum.Font.SourceSansBold
TabTrollBtn.TextSize = 13
TabTrollBtn.BorderSizePixel = 0

-- พื้นที่คอนเทนต์
local MainContainer = Instance.new("Frame", MainFrame)
MainContainer.Size = UDim2.new(1, 0, 1, -65)
MainContainer.Position = UDim2.new(0, 0, 0, 65)
MainContainer.BackgroundTransparency = 1

local TrollContainer = Instance.new("Frame", MainFrame)
TrollContainer.Size = UDim2.new(1, 0, 1, -65)
TrollContainer.Position = UDim2.new(0, 0, 0, 65)
TrollContainer.BackgroundTransparency = 1
TrollContainer.Visible = false

local function createButton(parent, text, yPos)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(0, 240, 0, 35)
    btn.Position = UDim2.new(0, 20, 0, yPos)
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 100, 100)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 14
    btn.BorderSizePixel = 0
    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0, 6)
    return btn
end

-- ปุ่มหมวดหมู่หลัก
local FlyBtn = createButton(MainContainer, "ระบบ บินอิสระ 360°: ปิดอยู่", 10)

local FlySpeedBox = Instance.new("TextBox", MainContainer)
FlySpeedBox.Size = UDim2.new(0, 240, 0, 30)
FlySpeedBox.Position = UDim2.new(0, 20, 0, 55)
FlySpeedBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
FlySpeedBox.PlaceholderText = "⚡ ความเร็วบิน (เริ่มต้น 50)..."
FlySpeedBox.Text = "50"
FlySpeedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
FlySpeedBox.Font = Enum.Font.SourceSans
FlySpeedBox.TextSize = 13
FlySpeedBox.BorderSizePixel = 0
local SpeedCorner = Instance.new("UICorner", FlySpeedBox)
SpeedCorner.CornerRadius = UDim.new(0, 6)

FlySpeedBox.FocusLost:Connect(function()
    local num = tonumber(FlySpeedBox.Text)
    if num then flySpeed = num else flySpeed = 50 FlySpeedBox.Text = "50" end
end)

local HitboxBtn = createButton(MainContainer, "ระบบ ขยาย Hitbox (หัวโต): ปิดอยู่", 95)

local HitboxSizeBox = Instance.new("TextBox", MainContainer)
HitboxSizeBox.Size = UDim2.new(0, 240, 0, 30)
HitboxSizeBox.Position = UDim2.new(0, 20, 0, 140)
HitboxSizeBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
HitboxSizeBox.PlaceholderText = "🎯 ขนาด Hitbox (เริ่มต้น 5)..."
HitboxSizeBox.Text = "5"
HitboxSizeBox.TextColor3 = Color3.fromRGB(255, 255, 255)
HitboxSizeBox.Font = Enum.Font.SourceSans
HitboxSizeBox.TextSize = 13
HitboxSizeBox.BorderSizePixel = 0
local HitboxCorner = Instance.new("UICorner", HitboxSizeBox)
HitboxCorner.CornerRadius = UDim.new(0, 6)

HitboxSizeBox.FocusLost:Connect(function()
    local num = tonumber(HitboxSizeBox.Text)
    if num then hitboxSize = num else hitboxSize = 5 HitboxSizeBox.Text = "5" end
end)

local EspBtn = createButton(MainContainer, "ระบบ มองทะลุ (ESP): ปิดอยู่", 180)
local SpeedBtn = createButton(MainContainer, "ระบบ วิ่งเร็ว: ปิดอยู่", 225)
local JumpBtn = createButton(MainContainer, "ระบบ กระโดดสูง: ปิดอยู่", 270)
local NoclipBtn = createButton(MainContainer, "ระบบ ทะลุกำแพง (Noclip): ปิดอยู่", 315)
local InvisBtn = createButton(MainContainer, "ระบบ ล่องหน: ปิดอยู่", 360)

-- ปุ่มหมวดหมู่ป่วน
local FollowBtn = createButton(TrollContainer, "ระบบ วาร์ปตามติดตัว: ปิดอยู่", 15)

local NameBox = Instance.new("TextBox", TrollContainer)
NameBox.Size = UDim2.new(0, 240, 0, 30)
NameBox.Position = UDim2.new(0, 20, 0, 65)
NameBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
NameBox.PlaceholderText = "👉 พิมพ์ชื่อคนที่จะตามตรงนี้..."
NameBox.Text = ""
NameBox.TextColor3 = Color3.fromRGB(255, 255, 255)
NameBox.Font = Enum.Font.SourceSans
NameBox.TextSize = 13
NameBox.BorderSizePixel = 0
local NameCorner = Instance.new("UICorner", NameBox)
NameCorner.CornerRadius = UDim.new(0, 6)

NameBox.FocusLost:Connect(function()
    targetName = NameBox.Text
end)

local PullBtn = createButton(TrollContainer, "ระบบ ดึงคนทั้งเซิร์ฟ: ปิดอยู่", 120)

-- สลับแท็บ
TabMainBtn.MouseButton1Click:Connect(function()
    currentCategory = "Main"
    MainContainer.Visible = true
    TrollContainer.Visible = false
    TabMainBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
    TabMainBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
    TabTrollBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    TabTrollBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
    TitleLabel.Text = "  zxtata | Fly & Hitbox Fix"
end)

TabTrollBtn.MouseButton1Click:Connect(function()
    currentCategory = "Troll"
    MainContainer.Visible = false
    TrollContainer.Visible = true
    TabTrollBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
    TabTrollBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
    TabMainBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    TabMainBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
    TitleLabel.Text = "  zxtata | หมวดหมู่ป่วน"
end)

-- ย่อ/ขยาย UI
local isMinimized = false
MinimizeBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    MainContainer.Visible = not isMinimized and (currentCategory == "Main")
    TrollContainer.Visible = not isMinimized and (currentCategory == "Troll")
    TabMainBtn.Visible = not isMinimized
    TabTrollBtn.Visible = not isMinimized
    MainFrame.Size = isMinimized and UDim2.new(0, 280, 0, 35) or UDim2.new(0, 280, 0, 480)
    MinimizeBtn.Text = isMinimized and "+" or "-"
end)

local function cleanupPlatform()
    if flyPlatform then
        flyPlatform:Destroy()
        flyPlatform = nil
    end
end

-- เปิด/ปิด บิน
FlyBtn.MouseButton1Click:Connect(function()
    flying = not flying
    FlyBtn.Text = flying and "ระบบ บินอิสระ 360°: เปิดใช้งาน" or "ระบบ บินอิสระ 360°: ปิดอยู่"
    FlyBtn.TextColor3 = flying and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    
    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChild("Humanoid")
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    
    if hrp and hum then
        if flying then
            flyPlatform = Instance.new("Part")
            flyPlatform.Size = Vector3.new(4, 1, 4)
            flyPlatform.Transparency = 1
            flyPlatform.Anchored = true
            flyPlatform.CFrame = hrp.CFrame * CFrame.new(0, -3.5, 0)
            flyPlatform.Parent = workspace
        else
            cleanupPlatform()
            hum.PlatformStand = false
        end
    end
end)

HitboxBtn.MouseButton1Click:Connect(function()
    hitboxEnabled = not hitboxEnabled
    HitboxBtn.Text = hitboxEnabled and "ระบบ ขยาย Hitbox (หัวโต): เปิดใช้งาน" or "ระบบ ขยาย Hitbox (หัวโต): ปิดอยู่"
    HitboxBtn.TextColor3 = hitboxEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    if not hitboxEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
                p.Character.Head.Size = Vector3.new(2, 1, 1)
                p.Character.Head.Transparency = 0
                p.Character.Head.CanCollide = true
            end
        end
    end
end)

EspBtn.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    EspBtn.Text = espEnabled and "ระบบ มองทะลุ (ESP): เปิดใช้งาน" or "ระบบ มองทะลุ (ESP): ปิดอยู่"
    EspBtn.TextColor3 = espEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    if not espEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p.Character and p.Character:FindFirstChild("Highlight") then
                p.Character.Highlight:Destroy()
            end
        end
    end
end)

SpeedBtn.MouseButton1Click:Connect(function()
    speedEnabled = not speedEnabled
    SpeedBtn.Text = speedEnabled and "ระบบ วิ่งเร็ว: เปิดใช้งาน" or "ระบบ วิ่งเร็ว: ปิดอยู่"
    SpeedBtn.TextColor3 = speedEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

JumpBtn.MouseButton1Click:Connect(function()
    jumpEnabled = not jumpEnabled
    JumpBtn.Text = jumpEnabled and "ระบบ กระโดดสูง: เปิดใช้งาน" or "ระบบ กระโดดสูง: ปิดอยู่"
    JumpBtn.TextColor3 = jumpEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

NoclipBtn.MouseButton1Click:Connect(function()
    noclipEnabled = not noclipEnabled
    NoclipBtn.Text = noclipEnabled and "ระบบ ทะลุกำแพง (Noclip): เปิดใช้งาน" or "ระบบ ทะลุกำแพง (Noclip): ปิดอยู่"
    NoclipBtn.TextColor3 = noclipEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

InvisBtn.MouseButton1Click:Connect(function()
    invisEnabled = not invisEnabled
    InvisBtn.Text = invisEnabled and "ระบบ ล่องหน: เปิดใช้งาน" or "ระบบ ล่องหน: ปิดอยู่"
    InvisBtn.TextColor3 = invisEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    
    local char = LocalPlayer.Character
    if char then
        if invisEnabled then
            for _, v in ipairs(char:GetDescendants()) do
                if v:IsA("BasePart") or v:IsA("Decal") then
                    savedParts[v] = v.Transparency
                    v.Transparency = 1
                end
            end
        else
            for part, originalTran in pairs(savedParts) do
                if part and part.Parent then part.Transparency = originalTran end
            end
            savedParts = {}
        end
    end
end)

FollowBtn.MouseButton1Click:Connect(function()
    following = not following
    FollowBtn.Text = following and "ระบบ วาร์ปตามติดตัว: เปิดใช้งาน" or "ระบบ วาร์ปตามติดตัว: ปิดอยู่"
    FollowBtn.TextColor3 = following and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

PullBtn.MouseButton1Click:Connect(function()
    pulling = not pulling
    PullBtn.Text = pulling and "ระบบ ดึงคนทั้งเซิร์ฟ: เปิดใช้งาน" or "ระบบ ดึงคนทั้งเซิร์ฟ: ปิดอยู่"
    PullBtn.TextColor3 = pulling and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

-- ลูปการทำงานเบื้องหลัง
RunService.Heartbeat:Connect(function(dt)
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") or not char:FindFirstChild("Humanoid") then return end
    local hrp = char.HumanoidRootPart
    local hum = char.Humanoid

    if speedEnabled then hum.WalkSpeed = speedVal else hum.WalkSpeed = 16 end
    if jumpEnabled then hum.JumpPower = jumpVal else hum.JumpPower = 50 end

    -- ระบบทะลุกำแพง (Noclip)
    if noclipEnabled then
        for _, part in ipairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end

    -- ระบบขยาย Hitbox (หัวโต)
    if hitboxEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
                local head = p.Character.Head
                head.Size = Vector3.new(hitboxSize, hitboxSize, hitboxSize)
                head.Transparency = 0.5
                head.CanCollide = false
            end
        end
    end

    -- บินอิสระ 360 องศา (แก้ทิศทางเรียบร้อย)
    if flying and flyPlatform then
        hum.PlatformStand = true
        local camCFrame = Camera.CFrame
        local moveDir = hum.MoveDirection
        
        local flyDirection = Vector3.new(0, 0, 0)
        if moveDir.Magnitude > 0 then
            flyDirection = (camCFrame.RightVector * moveDir.X + camCFrame.LookVector * -moveDir.Z).Unit
        else
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                flyDirection = camCFrame.LookVector
            end
        end

        flyPlatform.CFrame = flyPlatform.CFrame + (flyDirection * flySpeed * dt)
        hrp.CFrame = CFrame.new(flyPlatform.Position + Vector3.new(0, 3.5, 0), flyPlatform.Position + Vector3.new(0, 3.5, 0) + camCFrame.LookVector)
        hrp.Velocity = Vector3.new(0, 0, 0)
    end

    if espEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                if not p.Character:FindFirstChild("Highlight") then
                    local hl = Instance.new("Highlight", p.Character)
                    hl.FillColor = Color3.fromRGB(255, 0, 0)
                    hl.OutlineColor = Color3.fromRGB(255, 255, 255)
                end
            end
        end
    end

    if following and targetName ~= "" then
        local targetP = nil
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and (p.Name:lower():sub(1, #targetName) == targetName:lower() or p.DisplayName:lower():sub(1, #targetName) == targetName:lower()) then
                targetP = p
                break
            end
        end
        if targetP and targetP.Character and targetP.Character:FindFirstChild("HumanoidRootPart") then
            local targetHrp = targetP.Character.HumanoidRootPart
            if flying and flyPlatform then
                flyPlatform.CFrame = targetHrp.CFrame * CFrame.new(0, -3.5, 3)
            else
                hrp.CFrame = targetHrp.CFrame * CFrame.new(0, 0, 3)
            end
        end
    end

    if pulling then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local targetHrp = p.Character.HumanoidRootPart
                targetHrp.CFrame = hrp.CFrame * CFrame.new(0, 0, -3)
                targetHrp.Velocity = Vector3.new(0, 0, 0)
            end
        end
    end
end)

LocalPlayer.CharacterRemoving:Connect(function()
    cleanupPlatform()
end)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

-- ป้องกันหน้าต่างซ้อน
if LocalPlayer.PlayerGui:FindFirstChild("ZxtataGui") then
    LocalPlayer.PlayerGui.ZxtataGui:Destroy()
end

-- สถานะระบบ
local flying, espEnabled, speedEnabled, jumpEnabled, invisEnabled, noclipEnabled, hitboxEnabled = false, false, false, false, false, false, false
local following, pulling = false, false
local targetName = ""
local speedVal, jumpVal = 50, 100
local flySpeed = 50
local hitboxSize = 5
local currentCategory = "Main"
local savedParts = {}
local flyPlatform = nil

-- สร้างหน้าต่าง UI หลัก
local ScreenGui = Instance.new("ScreenGui", LocalPlayer.PlayerGui)
ScreenGui.Name = "ZxtataGui"
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 280, 0, 480)
MainFrame.Position = UDim2.new(0.5, -140, 0.3, -240)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.BorderSizePixel = 0

local FrameCorner = Instance.new("UICorner", MainFrame)
FrameCorner.CornerRadius = UDim.new(0, 8)

local TitleLabel = Instance.new("TextLabel", MainFrame)
TitleLabel.Size = UDim2.new(1, -30, 0, 35)
TitleLabel.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
TitleLabel.Text = "  zxtata | Fly 360° Fix"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.Font = Enum.Font.SourceSansBold
TitleLabel.TextSize = 14
TitleLabel.BorderSizePixel = 0

local TitleCorner = Instance.new("UICorner", TitleLabel)
TitleCorner.CornerRadius = UDim.new(0, 8)

local MinimizeBtn = Instance.new("TextButton", MainFrame)
MinimizeBtn.Size = UDim2.new(0, 30, 0, 35)
MinimizeBtn.Position = UDim2.new(1, -30, 0, 0)
MinimizeBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
MinimizeBtn.Text = "-"
MinimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeBtn.BorderSizePixel = 0

local MinCorner = Instance.new("UICorner", MinimizeBtn)
MinCorner.CornerRadius = UDim.new(0, 8)

-- ปุ่มสลับหมวดหมู่
local TabMainBtn = Instance.new("TextButton", MainFrame)
TabMainBtn.Size = UDim2.new(0.5, 0, 0, 30)
TabMainBtn.Position = UDim2.new(0, 0, 0, 35)
TabMainBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
TabMainBtn.Text = "หมวดหมู่หลัก"
TabMainBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
TabMainBtn.Font = Enum.Font.SourceSansBold
TabMainBtn.TextSize = 13
TabMainBtn.BorderSizePixel = 0

local TabTrollBtn = Instance.new("TextButton", MainFrame)
TabTrollBtn.Size = UDim2.new(0.5, 0, 0, 30)
TabTrollBtn.Position = UDim2.new(0.5, 0, 0, 35)
TabTrollBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TabTrollBtn.Text = "หมวดหมู่ป่วน"
TabTrollBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
TabTrollBtn.Font = Enum.Font.SourceSansBold
TabTrollBtn.TextSize = 13
TabTrollBtn.BorderSizePixel = 0

-- พื้นที่คอนเทนต์
local MainContainer = Instance.new("Frame", MainFrame)
MainContainer.Size = UDim2.new(1, 0, 1, -65)
MainContainer.Position = UDim2.new(0, 0, 0, 65)
MainContainer.BackgroundTransparency = 1

local TrollContainer = Instance.new("Frame", MainFrame)
TrollContainer.Size = UDim2.new(1, 0, 1, -65)
TrollContainer.Position = UDim2.new(0, 0, 0, 65)
TrollContainer.BackgroundTransparency = 1
TrollContainer.Visible = false

local function createButton(parent, text, yPos)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(0, 240, 0, 35)
    btn.Position = UDim2.new(0, 20, 0, yPos)
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 100, 100)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 14
    btn.BorderSizePixel = 0
    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0, 6)
    return btn
end

-- ปุ่มหมวดหมู่หลัก
local FlyBtn = createButton(MainContainer, "ระบบ บินอิสระ 360°: ปิดอยู่", 10)

local FlySpeedBox = Instance.new("TextBox", MainContainer)
FlySpeedBox.Size = UDim2.new(0, 240, 0, 30)
FlySpeedBox.Position = UDim2.new(0, 20, 0, 55)
FlySpeedBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
FlySpeedBox.PlaceholderText = "⚡ ความเร็วบิน (เริ่มต้น 50)..."
FlySpeedBox.Text = "50"
FlySpeedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
FlySpeedBox.Font = Enum.Font.SourceSans
FlySpeedBox.TextSize = 13
FlySpeedBox.BorderSizePixel = 0
local SpeedCorner = Instance.new("UICorner", FlySpeedBox)
SpeedCorner.CornerRadius = UDim.new(0, 6)

FlySpeedBox.FocusLost:Connect(function()
    local num = tonumber(FlySpeedBox.Text)
    if num then flySpeed = num else flySpeed = 50 FlySpeedBox.Text = "50" end
end)

local HitboxBtn = createButton(MainContainer, "ระบบ ขยาย Hitbox (หัวโต): ปิดอยู่", 100)

local HitboxSizeBox = Instance.new("TextBox", MainContainer)
HitboxSizeBox.Size = UDim2.new(0, 240, 0, 30)
HitboxSizeBox.Position = UDim2.new(0, 20, 0, 145)
HitboxSizeBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
HitboxSizeBox.PlaceholderText = "🎯 ขนาด Hitbox (เริ่มต้น 5)..."
HitboxSizeBox.Text = "5"
HitboxSizeBox.TextColor3 = Color3.fromRGB(255, 255, 255)
HitboxSizeBox.Font = Enum.Font.SourceSans
HitboxSizeBox.TextSize = 13
HitboxSizeBox.BorderSizePixel = 0
local HitboxCorner = Instance.new("UICorner", HitboxSizeBox)
HitboxCorner.CornerRadius = UDim.new(0, 6)

HitboxSizeBox.FocusLost:Connect(function()
    local num = tonumber(HitboxSizeBox.Text)
    if num then hitboxSize = num else hitboxSize = 5 HitboxSizeBox.Text = "5" end
end)

local EspBtn = createButton(MainContainer, "ระบบ มองทะลุ (ESP): ปิดอยู่", 190)
local SpeedBtn = createButton(MainContainer, "ระบบ วิ่งเร็ว: ปิดอยู่", 235)
local JumpBtn = createButton(MainContainer, "ระบบ กระโดดสูง: ปิดอยู่", 280)
local NoclipBtn = createButton(MainContainer, "ระบบ ทะลุกำแพง (Noclip): ปิดอยู่", 325)
local InvisBtn = createButton(MainContainer, "ระบบ ล่องหน: ปิดอยู่", 370)

-- ปุ่มหมวดหมู่ป่วน
local FollowBtn = createButton(TrollContainer, "ระบบ วาร์ปตามติดตัว: ปิดอยู่", 15)

local NameBox = Instance.new("TextBox", TrollContainer)
NameBox.Size = UDim2.new(0, 240, 0, 30)
NameBox.Position = UDim2.new(0, 20, 0, 65)
NameBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
NameBox.PlaceholderText = "👉 พิมพ์ชื่อคนที่จะตามตรงนี้..."
NameBox.Text = ""
NameBox.TextColor3 = Color3.fromRGB(255, 255, 255)
NameBox.Font = Enum.Font.SourceSans
NameBox.TextSize = 13
NameBox.BorderSizePixel = 0
local NameCorner = Instance.new("UICorner", NameBox)
NameCorner.CornerRadius = UDim.new(0, 6)

NameBox.FocusLost:Connect(function()
    targetName = NameBox.Text
end)

local PullBtn = createButton(TrollContainer, "ระบบ ดึงคนทั้งเซิร์ฟ: ปิดอยู่", 120)

-- สลับแท็บ
TabMainBtn.MouseButton1Click:Connect(function()
    currentCategory = "Main"
    MainContainer.Visible = true
    TrollContainer.Visible = false
    TabMainBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
    TabMainBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
    TabTrollBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    TabTrollBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
    TitleLabel.Text = "  zxtata | Fly 360° Fix"
end)

TabTrollBtn.MouseButton1Click:Connect(function()
    currentCategory = "Troll"
    MainContainer.Visible = false
    TrollContainer.Visible = true
    TabTrollBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
    TabTrollBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
    TabMainBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    TabMainBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
    TitleLabel.Text = "  zxtata | หมวดหมู่ป่วน"
end)

-- ย่อ/ขยาย UI
local isMinimized = false
MinimizeBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    MainContainer.Visible = not isMinimized and (currentCategory == "Main")
    TrollContainer.Visible = not isMinimized and (currentCategory == "Troll")
    TabMainBtn.Visible = not isMinimized
    TabTrollBtn.Visible = not isMinimized
    MainFrame.Size = isMinimized and UDim2.new(0, 280, 0, 35) or UDim2.new(0, 280, 0, 480)
    MinimizeBtn.Text = isMinimized and "+" or "-"
end)

local function cleanupPlatform()
    if flyPlatform then
        flyPlatform:Destroy()
        flyPlatform = nil
    end
end

-- เปิด/ปิด บิน
FlyBtn.MouseButton1Click:Connect(function()
    flying = not flying
    FlyBtn.Text = flying and "ระบบ บินอิสระ 360°: เปิดใช้งาน" or "ระบบ บินอิสระ 360°: ปิดอยู่"
    FlyBtn.TextColor3 = flying and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    
    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChild("Humanoid")
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    
    if hrp and hum then
        if flying then
            flyPlatform = Instance.new("Part")
            flyPlatform.Size = Vector3.new(4, 1, 4)
            flyPlatform.Transparency = 1
            flyPlatform.Anchored = true
            flyPlatform.CFrame = hrp.CFrame * CFrame.new(0, -3.5, 0)
            flyPlatform.Parent = workspace
        else
            cleanupPlatform()
            hum.PlatformStand = false
        end
    end
end)

HitboxBtn.MouseButton1Click:Connect(function()
    hitboxEnabled = not hitboxEnabled
    HitboxBtn.Text = hitboxEnabled and "ระบบ ขยาย Hitbox (หัวโต): เปิดใช้งาน" or "ระบบ ขยาย Hitbox (หัวโต): ปิดอยู่"
    HitboxBtn.TextColor3 = hitboxEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    if not hitboxEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
                p.Character.Head.Size = Vector3.new(2, 1, 1)
                p.Character.Head.Transparency = 0
                p.Character.Head.CanCollide = true
            end
        end
    end
end)

EspBtn.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    EspBtn.Text = espEnabled and "ระบบ มองทะลุ (ESP): เปิดใช้งาน" or "ระบบ มองทะลุ (ESP): ปิดอยู่"
    EspBtn.TextColor3 = espEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    if not espEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p.Character and p.Character:FindFirstChild("Highlight") then
                p.Character.Highlight:Destroy()
            end
        end
    end
end)

SpeedBtn.MouseButton1Click:Connect(function()
    speedEnabled = not speedEnabled
    SpeedBtn.Text = speedEnabled and "ระบบ วิ่งเร็ว: เปิดใช้งาน" or "ระบบ วิ่งเร็ว: ปิดอยู่"
    SpeedBtn.TextColor3 = speedEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

JumpBtn.MouseButton1Click:Connect(function()
    jumpEnabled = not jumpEnabled
    JumpBtn.Text = jumpEnabled and "ระบบ กระโดดสูง: เปิดใช้งาน" or "ระบบ กระโดดสูง: ปิดอยู่"
    JumpBtn.TextColor3 = jumpEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

NoclipBtn.MouseButton1Click:Connect(function()
    noclipEnabled = not noclipEnabled
    NoclipBtn.Text = noclipEnabled and "ระบบ ทะลุกำแพง (Noclip): เปิดใช้งาน" or "ระบบ ทะลุกำแพง (Noclip): ปิดอยู่"
    NoclipBtn.TextColor3 = noclipEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

InvisBtn.MouseButton1Click:Connect(function()
    invisEnabled = not invisEnabled
    InvisBtn.Text = invisEnabled and "ระบบ ล่องหน: เปิดใช้งาน" or "ระบบ ล่องหน: ปิดอยู่"
    InvisBtn.TextColor3 = invisEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    
    local char = LocalPlayer.Character
    if char then
        if invisEnabled then
            for _, v in ipairs(char:GetDescendants()) do
                if v:IsA("BasePart") or v:IsA("Decal") then
                    savedParts[v] = v.Transparency
                    v.Transparency = 1
                end
            end
        else
            for part, originalTran in pairs(savedParts) do
                if part and part.Parent then part.Transparency = originalTran end
            end
            savedParts = {}
        end
    end
end)

FollowBtn.MouseButton1Click:Connect(function()
    following = not following
    FollowBtn.Text = following and "ระบบ วาร์ปตามติดตัว: เปิดใช้งาน" or "ระบบ วาร์ปตามติดตัว: ปิดอยู่"
    FollowBtn.TextColor3 = following and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

PullBtn.MouseButton1Click:Connect(function()
    pulling = not pulling
    PullBtn.Text = pulling and "ระบบ ดึงคนทั้งเซิร์ฟ: เปิดใช้งาน" or "ระบบ ดึงคนทั้งเซิร์ฟ: ปิดอยู่"
    PullBtn.TextColor3 = pulling and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

-- ลูปการทำงานเบื้องหลัง
RunService.Heartbeat:Connect(function(dt)
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") or not char:FindFirstChild("Humanoid") then return end
    local hrp = char.HumanoidRootPart
    local hum = char.Humanoid

    if speedEnabled then hum.WalkSpeed = speedVal else hum.WalkSpeed = 16 end
    if jumpEnabled then hum.JumpPower = jumpVal else hum.JumpPower = 50 end

    -- ระบบทะลุกำแพง (Noclip)
    if noclipEnabled then
        for _, part in ipairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end

    -- ระบบขยาย Hitbox
    if hitboxEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
                local head = p.Character.Head
                head.Size = Vector3.new(hitboxSize, hitboxSize, hitboxSize)
                head.Transparency = 0.5
                head.CanCollide = false
            end
        end
    end

    -- บินอิสระ 360 องศา (แก้ทิศทางตรงกันข้ามเรียบร้อย)
    if flying and flyPlatform then
        hum.PlatformStand = true
        local camCFrame = Camera.CFrame
        local moveDir = hum.MoveDirection
        
        local flyDirection = Vector3.new(0, 0, 0)
        if moveDir.Magnitude > 0 then
            -- แก้เครื่องหมาย LookVector ให้ถูกต้องตามการดันจอยและมุมกล้อง
            flyDirection = (camCFrame.RightVector * moveDir.X + camCFrame.LookVector * -moveDir.Z).Unit
        else
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                flyDirection = camCFrame.LookVector
            end
        end

        flyPlatform.CFrame = flyPlatform.CFrame + (flyDirection * flySpeed * dt)
        hrp.CFrame = CFrame.new(flyPlatform.Position + Vector3.new(0, 3.5, 0), flyPlatform.Position + Vector3.new(0, 3.5, 0) + camCFrame.LookVector)
        hrp.Velocity = Vector3.new(0, 0, 0)
    end

    if espEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                if not p.Character:FindFirstChild("Highlight") then
                    local hl = Instance.new("Highlight", p.Character)
                    hl.FillColor = Color3.fromRGB(255, 0, 0)
                    hl.OutlineColor = Color3.fromRGB(255, 255, 255)
                end
            end
        end
    end

    if following and targetName ~= "" then
        local targetP = nil
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and (p.Name:lower():sub(1, #targetName) == targetName:lower() or p.DisplayName:lower():sub(1, #targetName) == targetName:lower()) then
                targetP = p
                break
            end
        end
        if targetP and targetP.Character and targetP.Character:FindFirstChild("HumanoidRootPart") then
            local targetHrp = targetP.Character.HumanoidRootPart
            if flying and flyPlatform then
                flyPlatform.CFrame = targetHrp.CFrame * CFrame.new(0, -3.5, 3)
            else
                hrp.CFrame = targetHrp.CFrame * CFrame.new(0, 0, 3)
            end
        end
    end

    if pulling then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local targetHrp = p.Character.HumanoidRootPart
                targetHrp.CFrame = hrp.CFrame * CFrame.new(0, 0, -3)
                targetHrp.Velocity = Vector3.new(0, 0, 0)
            end
        end
    end
end)

LocalPlayer.CharacterRemoving:Connect(function()
    cleanupPlatform()
end)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

-- ป้องกันหน้าต่างซ้อน
if LocalPlayer.PlayerGui:FindFirstChild("ZxtataGui") then
    LocalPlayer.PlayerGui.ZxtataGui:Destroy()
end

-- สถานะระบบ
local flying, espEnabled, speedEnabled, jumpEnabled, invisEnabled, noclipEnabled = false, false, false, false, false, false
local following, pulling = false, false
local targetName = ""
local speedVal, jumpVal = 50, 100
local flySpeed = 50
local currentCategory = "Main"
local savedParts = {}
local flyPlatform = nil

-- สร้างหน้าต่าง UI หลัก
local ScreenGui = Instance.new("ScreenGui", LocalPlayer.PlayerGui)
ScreenGui.Name = "ZxtataGui"
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 280, 0, 435)
MainFrame.Position = UDim2.new(0.5, -140, 0.3, -217)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.BorderSizePixel = 0

local FrameCorner = Instance.new("UICorner", MainFrame)
FrameCorner.CornerRadius = UDim.new(0, 8)

local TitleLabel = Instance.new("TextLabel", MainFrame)
TitleLabel.Size = UDim2.new(1, -30, 0, 35)
TitleLabel.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
TitleLabel.Text = "  zxtata | เมนูหลัก (360° Fix)"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.Font = Enum.Font.SourceSansBold
TitleLabel.TextSize = 14
TitleLabel.BorderSizePixel = 0

local TitleCorner = Instance.new("UICorner", TitleLabel)
TitleCorner.CornerRadius = UDim.new(0, 8)

local MinimizeBtn = Instance.new("TextButton", MainFrame)
MinimizeBtn.Size = UDim2.new(0, 30, 0, 35)
MinimizeBtn.Position = UDim2.new(1, -30, 0, 0)
MinimizeBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
MinimizeBtn.Text = "-"
MinimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeBtn.BorderSizePixel = 0

local MinCorner = Instance.new("UICorner", MinimizeBtn)
MinCorner.CornerRadius = UDim.new(0, 8)

-- ปุ่มสลับหมวดหมู่
local TabMainBtn = Instance.new("TextButton", MainFrame)
TabMainBtn.Size = UDim2.new(0.5, 0, 0, 30)
TabMainBtn.Position = UDim2.new(0, 0, 0, 35)
TabMainBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
TabMainBtn.Text = "หมวดหมู่หลัก"
TabMainBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
TabMainBtn.Font = Enum.Font.SourceSansBold
TabMainBtn.TextSize = 13
TabMainBtn.BorderSizePixel = 0

local TabTrollBtn = Instance.new("TextButton", MainFrame)
TabTrollBtn.Size = UDim2.new(0.5, 0, 0, 30)
TabTrollBtn.Position = UDim2.new(0.5, 0, 0, 35)
TabTrollBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TabTrollBtn.Text = "หมวดหมู่ป่วน"
TabTrollBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
TabTrollBtn.Font = Enum.Font.SourceSansBold
TabTrollBtn.TextSize = 13
TabTrollBtn.BorderSizePixel = 0

-- พื้นที่คอนเทนต์
local MainContainer = Instance.new("Frame", MainFrame)
MainContainer.Size = UDim2.new(1, 0, 1, -65)
MainContainer.Position = UDim2.new(0, 0, 0, 65)
MainContainer.BackgroundTransparency = 1

local TrollContainer = Instance.new("Frame", MainFrame)
TrollContainer.Size = UDim2.new(1, 0, 1, -65)
TrollContainer.Position = UDim2.new(0, 0, 0, 65)
TrollContainer.BackgroundTransparency = 1
TrollContainer.Visible = false

local function createButton(parent, text, yPos)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(0, 240, 0, 35)
    btn.Position = UDim2.new(0, 20, 0, yPos)
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 100, 100)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 14
    btn.BorderSizePixel = 0
    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0, 6)
    return btn
end

-- ปุ่มหมวดหมู่หลัก
local FlyBtn = createButton(MainContainer, "ระบบ บินอิสระ 360°: ปิดอยู่", 10)

local FlySpeedBox = Instance.new("TextBox", MainContainer)
FlySpeedBox.Size = UDim2.new(0, 240, 0, 30)
FlySpeedBox.Position = UDim2.new(0, 20, 0, 55)
FlySpeedBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
FlySpeedBox.PlaceholderText = "⚡ ความเร็วบิน (เริ่มต้น 50)..."
FlySpeedBox.Text = "50"
FlySpeedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
FlySpeedBox.Font = Enum.Font.SourceSans
FlySpeedBox.TextSize = 13
FlySpeedBox.BorderSizePixel = 0
local SpeedCorner = Instance.new("UICorner", FlySpeedBox)
SpeedCorner.CornerRadius = UDim.new(0, 6)

FlySpeedBox.FocusLost:Connect(function()
    local num = tonumber(FlySpeedBox.Text)
    if num then flySpeed = num else flySpeed = 50 FlySpeedBox.Text = "50" end
end)

local EspBtn = createButton(MainContainer, "ระบบ มองทะลุ (ESP): ปิดอยู่", 100)
local SpeedBtn = createButton(MainContainer, "ระบบ วิ่งเร็ว: ปิดอยู่", 145)
local JumpBtn = createButton(MainContainer, "ระบบ กระโดดสูง: ปิดอยู่", 190)
local NoclipBtn = createButton(MainContainer, "ระบบ ทะลุกำแพง (Noclip): ปิดอยู่", 235)
local InvisBtn = createButton(MainContainer, "ระบบ ล่องหน: ปิดอยู่", 280)

-- ปุ่มหมวดหมู่ป่วน
local FollowBtn = createButton(TrollContainer, "ระบบ วาร์ปตามติดตัว: ปิดอยู่", 15)

local NameBox = Instance.new("TextBox", TrollContainer)
NameBox.Size = UDim2.new(0, 240, 0, 30)
NameBox.Position = UDim2.new(0, 20, 0, 65)
NameBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
NameBox.PlaceholderText = "👉 พิมพ์ชื่อคนที่จะตามตรงนี้..."
NameBox.Text = ""
NameBox.TextColor3 = Color3.fromRGB(255, 255, 255)
NameBox.Font = Enum.Font.SourceSans
NameBox.TextSize = 13
NameBox.BorderSizePixel = 0
local NameCorner = Instance.new("UICorner", NameBox)
NameCorner.CornerRadius = UDim.new(0, 6)

NameBox.FocusLost:Connect(function()
    targetName = NameBox.Text
end)

local PullBtn = createButton(TrollContainer, "ระบบ ดึงคนทั้งเซิร์ฟ: ปิดอยู่", 120)

-- สลับแท็บ
TabMainBtn.MouseButton1Click:Connect(function()
    currentCategory = "Main"
    MainContainer.Visible = true
    TrollContainer.Visible = false
    TabMainBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
    TabMainBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
    TabTrollBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    TabTrollBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
    TitleLabel.Text = "  zxtata | เมนูหลัก (360° Fix)"
end)

TabTrollBtn.MouseButton1Click:Connect(function()
    currentCategory = "Troll"
    MainContainer.Visible = false
    TrollContainer.Visible = true
    TabTrollBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
    TabTrollBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
    TabMainBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    TabMainBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
    TitleLabel.Text = "  zxtata | หมวดหมู่ป่วน"
end)

-- ย่อ/ขยาย UI
local isMinimized = false
MinimizeBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    MainContainer.Visible = not isMinimized and (currentCategory == "Main")
    TrollContainer.Visible = not isMinimized and (currentCategory == "Troll")
    TabMainBtn.Visible = not isMinimized
    TabTrollBtn.Visible = not isMinimized
    MainFrame.Size = isMinimized and UDim2.new(0, 280, 0, 35) or UDim2.new(0, 280, 0, 435)
    MinimizeBtn.Text = isMinimized and "+" or "-"
end)

local function cleanupPlatform()
    if flyPlatform then
        flyPlatform:Destroy()
        flyPlatform = nil
    end
end

-- เปิด/ปิด บิน
FlyBtn.MouseButton1Click:Connect(function()
    flying = not flying
    FlyBtn.Text = flying and "ระบบ บินอิสระ 360°: เปิดใช้งาน" or "ระบบ บินอิสระ 360°: ปิดอยู่"
    FlyBtn.TextColor3 = flying and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    
    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChild("Humanoid")
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    
    if hrp and hum then
        if flying then
            flyPlatform = Instance.new("Part")
            flyPlatform.Size = Vector3.new(4, 1, 4)
            flyPlatform.Transparency = 1
            flyPlatform.Anchored = true
            flyPlatform.CFrame = hrp.CFrame * CFrame.new(0, -3.5, 0)
            flyPlatform.Parent = workspace
        else
            cleanupPlatform()
            hum.PlatformStand = false
        end
    end
end)

EspBtn.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    EspBtn.Text = espEnabled and "ระบบ มองทะลุ (ESP): เปิดใช้งาน" or "ระบบ มองทะลุ (ESP): ปิดอยู่"
    EspBtn.TextColor3 = espEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    if not espEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p.Character and p.Character:FindFirstChild("Highlight") then
                p.Character.Highlight:Destroy()
            end
        end
    end
end)

SpeedBtn.MouseButton1Click:Connect(function()
    speedEnabled = not speedEnabled
    SpeedBtn.Text = speedEnabled and "ระบบ วิ่งเร็ว: เปิดใช้งาน" or "ระบบ วิ่งเร็ว: ปิดอยู่"
    SpeedBtn.TextColor3 = speedEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

JumpBtn.MouseButton1Click:Connect(function()
    jumpEnabled = not jumpEnabled
    JumpBtn.Text = jumpEnabled and "ระบบ กระโดดสูง: เปิดใช้งาน" or "ระบบ กระโดดสูง: ปิดอยู่"
    JumpBtn.TextColor3 = jumpEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

NoclipBtn.MouseButton1Click:Connect(function()
    noclipEnabled = not noclipEnabled
    NoclipBtn.Text = noclipEnabled and "ระบบ ทะลุกำแพง (Noclip): เปิดใช้งาน" or "ระบบ ทะลุกำแพง (Noclip): ปิดอยู่"
    NoclipBtn.TextColor3 = noclipEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

InvisBtn.MouseButton1Click:Connect(function()
    invisEnabled = not invisEnabled
    InvisBtn.Text = invisEnabled and "ระบบ ล่องหน: เปิดใช้งาน" or "ระบบ ล่องหน: ปิดอยู่"
    InvisBtn.TextColor3 = invisEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    
    local char = LocalPlayer.Character
    if char then
        if invisEnabled then
            for _, v in ipairs(char:GetDescendants()) do
                if v:IsA("BasePart") or v:IsA("Decal") then
                    savedParts[v] = v.Transparency
                    v.Transparency = 1
                end
            end
        else
            for part, originalTran in pairs(savedParts) do
                if part and part.Parent then part.Transparency = originalTran end
            end
            savedParts = {}
        end
    end
end)

FollowBtn.MouseButton1Click:Connect(function()
    following = not following
    FollowBtn.Text = following and "ระบบ วาร์ปตามติดตัว: เปิดใช้งาน" or "ระบบ วาร์ปตามติดตัว: ปิดอยู่"
    FollowBtn.TextColor3 = following and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

PullBtn.MouseButton1Click:Connect(function()
    pulling = not pulling
    PullBtn.Text = pulling and "ระบบ ดึงคนทั้งเซิร์ฟ: เปิดใช้งาน" or "ระบบ ดึงคนทั้งเซิร์ฟ: ปิดอยู่"
    PullBtn.TextColor3 = pulling and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

-- ลูปการทำงานเบื้องหลัง (แก้ทิศทางเดินถอยหลังให้ถูกต้อง)
RunService.Heartbeat:Connect(function(dt)
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") or not char:FindFirstChild("Humanoid") then return end
    local hrp = char.HumanoidRootPart
    local hum = char.Humanoid

    if speedEnabled then hum.WalkSpeed = speedVal else hum.WalkSpeed = 16 end
    if jumpEnabled then hum.JumpPower = jumpVal else hum.JumpPower = 50 end

    -- ระบบทะลุกำแพง (Noclip)
    if noclipEnabled then
        for _, part in ipairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end

    -- บินอิสระ 360 องศา (แก้ทิศทางเดินหน้า/ถอยหลังให้ตรงกับจอย)
    if flying and flyPlatform then
        hum.PlatformStand = true
        local camCFrame = Camera.CFrame
        local moveDir = hum.MoveDirection
        
        local flyDirection = Vector3.new(0, 0, 0)
        if moveDir.Magnitude > 0 then
            -- แก้เครื่องหมาย LookVector เป็นบวก เพื่อให้ถอยหลังคือถอยหลังตามจอย
            flyDirection = (camCFrame.RightVector * moveDir.X + camCFrame.LookVector * moveDir.Z).Unit
        else
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                flyDirection = camCFrame.LookVector
            end
        end

        flyPlatform.CFrame = flyPlatform.CFrame + (flyDirection * flySpeed * dt)
        hrp.CFrame = CFrame.new(flyPlatform.Position + Vector3.new(0, 3.5, 0), flyPlatform.Position + Vector3.new(0, 3.5, 0) + camCFrame.LookVector)
        hrp.Velocity = Vector3.new(0, 0, 0)
    end

    if espEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                if not p.Character:FindFirstChild("Highlight") then
                    local hl = Instance.new("Highlight", p.Character)
                    hl.FillColor = Color3.fromRGB(255, 0, 0)
                    hl.OutlineColor = Color3.fromRGB(255, 255, 255)
                end
            end
        end
    end

    if following and targetName ~= "" then
        local targetP = nil
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and (p.Name:lower():sub(1, #targetName) == targetName:lower() or p.DisplayName:lower():sub(1, #targetName) == targetName:lower()) then
                targetP = p
                break
            end
        end
        if targetP and targetP.Character and targetP.Character:FindFirstChild("HumanoidRootPart") then
            local targetHrp = targetP.Character.HumanoidRootPart
            if flying and flyPlatform then
                flyPlatform.CFrame = targetHrp.CFrame * CFrame.new(0, -3.5, 3)
            else
                hrp.CFrame = targetHrp.CFrame * CFrame.new(0, 0, 3)
            end
        end
    end

    if pulling then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local targetHrp = p.Character.HumanoidRootPart
                targetHrp.CFrame = hrp.CFrame * CFrame.new(0, 0, -3)
                targetHrp.Velocity = Vector3.new(0, 0, 0)
            end
        end
    end
end)

LocalPlayer.CharacterRemoving:Connect(function()
    cleanupPlatform()
end)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

-- ป้องกันหน้าต่างซ้อน
if LocalPlayer.PlayerGui:FindFirstChild("ZxtataGui") then
    LocalPlayer.PlayerGui.ZxtataGui:Destroy()
end

-- สถานะระบบ (เพิ่ม noclip เข้าไปแล้ว)
local flying, espEnabled, speedEnabled, jumpEnabled, invisEnabled, noclipEnabled = false, false, false, false, false, false
local following, pulling = false, false
local targetName = ""
local speedVal, jumpVal = 50, 100
local flySpeed = 50
local currentCategory = "Main"
local savedParts = {}
local flyPlatform = nil

-- สร้างหน้าต่าง UI หลัก
local ScreenGui = Instance.new("ScreenGui", LocalPlayer.PlayerGui)
ScreenGui.Name = "ZxtataGui"
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 280, 0, 435)
MainFrame.Position = UDim2.new(0.5, -140, 0.3, -217)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.BorderSizePixel = 0

local FrameCorner = Instance.new("UICorner", MainFrame)
FrameCorner.CornerRadius = UDim.new(0, 8)

local TitleLabel = Instance.new("TextLabel", MainFrame)
TitleLabel.Size = UDim2.new(1, -30, 0, 35)
TitleLabel.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
TitleLabel.Text = "  zxtata | เมนูหลัก (360° & Noclip)"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.Font = Enum.Font.SourceSansBold
TitleLabel.TextSize = 14
TitleLabel.BorderSizePixel = 0

local TitleCorner = Instance.new("UICorner", TitleLabel)
TitleCorner.CornerRadius = UDim.new(0, 8)

local MinimizeBtn = Instance.new("TextButton", MainFrame)
MinimizeBtn.Size = UDim2.new(0, 30, 0, 35)
MinimizeBtn.Position = UDim2.new(1, -30, 0, 0)
MinimizeBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
MinimizeBtn.Text = "-"
MinimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeBtn.BorderSizePixel = 0

local MinCorner = Instance.new("UICorner", MinimizeBtn)
MinCorner.CornerRadius = UDim.new(0, 8)

-- ปุ่มสลับหมวดหมู่
local TabMainBtn = Instance.new("TextButton", MainFrame)
TabMainBtn.Size = UDim2.new(0.5, 0, 0, 30)
TabMainBtn.Position = UDim2.new(0, 0, 0, 35)
TabMainBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
TabMainBtn.Text = "หมวดหมู่หลัก"
TabMainBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
TabMainBtn.Font = Enum.Font.SourceSansBold
TabMainBtn.TextSize = 13
TabMainBtn.BorderSizePixel = 0

local TabTrollBtn = Instance.new("TextButton", MainFrame)
TabTrollBtn.Size = UDim2.new(0.5, 0, 0, 30)
TabTrollBtn.Position = UDim2.new(0.5, 0, 0, 35)
TabTrollBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TabTrollBtn.Text = "หมวดหมู่ป่วน"
TabTrollBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
TabTrollBtn.Font = Enum.Font.SourceSansBold
TabTrollBtn.TextSize = 13
TabTrollBtn.BorderSizePixel = 0

-- พื้นที่คอนเทนต์
local MainContainer = Instance.new("Frame", MainFrame)
MainContainer.Size = UDim2.new(1, 0, 1, -65)
MainContainer.Position = UDim2.new(0, 0, 0, 65)
MainContainer.BackgroundTransparency = 1

local TrollContainer = Instance.new("Frame", MainFrame)
TrollContainer.Size = UDim2.new(1, 0, 1, -65)
TrollContainer.Position = UDim2.new(0, 0, 0, 65)
TrollContainer.BackgroundTransparency = 1
TrollContainer.Visible = false

local function createButton(parent, text, yPos)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(0, 240, 0, 35)
    btn.Position = UDim2.new(0, 20, 0, yPos)
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 100, 100)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 14
    btn.BorderSizePixel = 0
    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0, 6)
    return btn
end

-- ปุ่มหมวดหมู่หลัก
local FlyBtn = createButton(MainContainer, "ระบบ บินอิสระ 360°: ปิดอยู่", 10)

local FlySpeedBox = Instance.new("TextBox", MainContainer)
FlySpeedBox.Size = UDim2.new(0, 240, 0, 30)
FlySpeedBox.Position = UDim2.new(0, 20, 0, 55)
FlySpeedBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
FlySpeedBox.PlaceholderText = "⚡ ความเร็วบิน (เริ่มต้น 50)..."
FlySpeedBox.Text = "50"
FlySpeedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
FlySpeedBox.Font = Enum.Font.SourceSans
FlySpeedBox.TextSize = 13
FlySpeedBox.BorderSizePixel = 0
local SpeedCorner = Instance.new("UICorner", FlySpeedBox)
SpeedCorner.CornerRadius = UDim.new(0, 6)

FlySpeedBox.FocusLost:Connect(function()
    local num = tonumber(FlySpeedBox.Text)
    if num then flySpeed = num else flySpeed = 50 FlySpeedBox.Text = "50" end
end)

local EspBtn = createButton(MainContainer, "ระบบ มองทะลุ (ESP): ปิดอยู่", 100)
local SpeedBtn = createButton(MainContainer, "ระบบ วิ่งเร็ว: ปิดอยู่", 145)
local JumpBtn = createButton(MainContainer, "ระบบ กระโดดสูง: ปิดอยู่", 190)
local NoclipBtn = createButton(MainContainer, "ระบบ ทะลุกำแพง (Noclip): ปิดอยู่", 235)
local InvisBtn = createButton(MainContainer, "ระบบ ล่องหน: ปิดอยู่", 280)

-- ปุ่มหมวดหมู่ป่วน
local FollowBtn = createButton(TrollContainer, "ระบบ วาร์ปตามติดตัว: ปิดอยู่", 15)

local NameBox = Instance.new("TextBox", TrollContainer)
NameBox.Size = UDim2.new(0, 240, 0, 30)
NameBox.Position = UDim2.new(0, 20, 0, 65)
NameBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
NameBox.PlaceholderText = "👉 พิมพ์ชื่อคนที่จะตามตรงนี้..."
NameBox.Text = ""
NameBox.TextColor3 = Color3.fromRGB(255, 255, 255)
NameBox.Font = Enum.Font.SourceSans
NameBox.TextSize = 13
NameBox.BorderSizePixel = 0
local NameCorner = Instance.new("UICorner", NameBox)
NameCorner.CornerRadius = UDim.new(0, 6)

NameBox.FocusLost:Connect(function()
    targetName = NameBox.Text
end)

local PullBtn = createButton(TrollContainer, "ระบบ ดึงคนทั้งเซิร์ฟ: ปิดอยู่", 120)

-- สลับแท็บ
TabMainBtn.MouseButton1Click:Connect(function()
    currentCategory = "Main"
    MainContainer.Visible = true
    TrollContainer.Visible = false
    TabMainBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
    TabMainBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
    TabTrollBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    TabTrollBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
    TitleLabel.Text = "  zxtata | เมนูหลัก (360° & Noclip)"
end)

TabTrollBtn.MouseButton1Click:Connect(function()
    currentCategory = "Troll"
    MainContainer.Visible = false
    TrollContainer.Visible = true
    TabTrollBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
    TabTrollBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
    TabMainBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    TabMainBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
    TitleLabel.Text = "  zxtata | หมวดหมู่ป่วน"
end)

-- ย่อ/ขยาย UI
local isMinimized = false
MinimizeBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    MainContainer.Visible = not isMinimized and (currentCategory == "Main")
    TrollContainer.Visible = not isMinimized and (currentCategory == "Troll")
    TabMainBtn.Visible = not isMinimized
    TabTrollBtn.Visible = not isMinimized
    MainFrame.Size = isMinimized and UDim2.new(0, 280, 0, 35) or UDim2.new(0, 280, 0, 435)
    MinimizeBtn.Text = isMinimized and "+" or "-"
end)

local function cleanupPlatform()
    if flyPlatform then
        flyPlatform:Destroy()
        flyPlatform = nil
    end
end

-- เปิด/ปิด บิน
FlyBtn.MouseButton1Click:Connect(function()
    flying = not flying
    FlyBtn.Text = flying and "ระบบ บินอิสระ 360°: เปิดใช้งาน" or "ระบบ บินอิสระ 360°: ปิดอยู่"
    FlyBtn.TextColor3 = flying and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    
    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChild("Humanoid")
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    
    if hrp and hum then
        if flying then
            flyPlatform = Instance.new("Part")
            flyPlatform.Size = Vector3.new(4, 1, 4)
            flyPlatform.Transparency = 1
            flyPlatform.Anchored = true
            flyPlatform.CFrame = hrp.CFrame * CFrame.new(0, -3.5, 0)
            flyPlatform.Parent = workspace
        else
            cleanupPlatform()
            hum.PlatformStand = false
        end
    end
end)

EspBtn.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    EspBtn.Text = espEnabled and "ระบบ มองทะลุ (ESP): เปิดใช้งาน" or "ระบบ มองทะลุ (ESP): ปิดอยู่"
    EspBtn.TextColor3 = espEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    if not espEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p.Character and p.Character:FindFirstChild("Highlight") then
                p.Character.Highlight:Destroy()
            end
        end
    end
end)

SpeedBtn.MouseButton1Click:Connect(function()
    speedEnabled = not speedEnabled
    SpeedBtn.Text = speedEnabled and "ระบบ วิ่งเร็ว: เปิดใช้งาน" or "ระบบ วิ่งเร็ว: ปิดอยู่"
    SpeedBtn.TextColor3 = speedEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

JumpBtn.MouseButton1Click:Connect(function()
    jumpEnabled = not jumpEnabled
    JumpBtn.Text = jumpEnabled and "ระบบ กระโดดสูง: เปิดใช้งาน" or "ระบบ กระโดดสูง: ปิดอยู่"
    JumpBtn.TextColor3 = jumpEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

NoclipBtn.MouseButton1Click:Connect(function()
    noclipEnabled = not noclipEnabled
    NoclipBtn.Text = noclipEnabled and "ระบบ ทะลุกำแพง (Noclip): เปิดใช้งาน" or "ระบบ ทะลุกำแพง (Noclip): ปิดอยู่"
    NoclipBtn.TextColor3 = noclipEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

InvisBtn.MouseButton1Click:Connect(function()
    invisEnabled = not invisEnabled
    InvisBtn.Text = invisEnabled and "ระบบ ล่องหน: เปิดใช้งาน" or "ระบบ ล่องหน: ปิดอยู่"
    InvisBtn.TextColor3 = invisEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    
    local char = LocalPlayer.Character
    if char then
        if invisEnabled then
            for _, v in ipairs(char:GetDescendants()) do
                if v:IsA("BasePart") or v:IsA("Decal") then
                    savedParts[v] = v.Transparency
                    v.Transparency = 1
                end
            end
        else
            for part, originalTran in pairs(savedParts) do
                if part and part.Parent then part.Transparency = originalTran end
            end
            savedParts = {}
        end
    end
end)

FollowBtn.MouseButton1Click:Connect(function()
    following = not following
    FollowBtn.Text = following and "ระบบ วาร์ปตามติดตัว: เปิดใช้งาน" or "ระบบ วาร์ปตามติดตัว: ปิดอยู่"
    FollowBtn.TextColor3 = following and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

PullBtn.MouseButton1Click:Connect(function()
    pulling = not pulling
    PullBtn.Text = pulling and "ระบบ ดึงคนทั้งเซิร์ฟ: เปิดใช้งาน" or "ระบบ ดึงคนทั้งเซิร์ฟ: ปิดอยู่"
    PullBtn.TextColor3 = pulling and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

-- ลูปการทำงานเบื้องหลัง (แก้กระตุกโดยใช้ Heartbeat และคำนวณ CFrame สมูทๆ)
RunService.Heartbeat:Connect(function(dt)
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") or not char:FindFirstChild("Humanoid") then return end
    local hrp = char.HumanoidRootPart
    local hum = char.Humanoid

    if speedEnabled then hum.WalkSpeed = speedVal else hum.WalkSpeed = 16 end
    if jumpEnabled then hum.JumpPower = jumpVal else hum.JumpPower = 50 end

    -- ระบบทะลุกำแพง (Noclip)
    if noclipEnabled then
        for _, part in ipairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end

    -- บินอิสระ 360 องศา (แบบไม่กระตุก)
    if flying and flyPlatform then
        hum.PlatformStand = true
        local camCFrame = Camera.CFrame
        local moveDir = hum.MoveDirection
        
        local flyDirection = Vector3.new(0, 0, 0)
        if moveDir.Magnitude > 0 then
            flyDirection = (camCFrame.RightVector * moveDir.X + camCFrame.LookVector * -moveDir.Z).Unit
        else
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                flyDirection = camCFrame.LookVector
            end
        end

        flyPlatform.CFrame = flyPlatform.CFrame + (flyDirection * flySpeed * dt)
        hrp.CFrame = CFrame.new(flyPlatform.Position + Vector3.new(0, 3.5, 0), flyPlatform.Position + Vector3.new(0, 3.5, 0) + camCFrame.LookVector)
        hrp.Velocity = Vector3.new(0, 0, 0)
    end

    if espEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                if not p.Character:FindFirstChild("Highlight") then
                    local hl = Instance.new("Highlight", p.Character)
                    hl.FillColor = Color3.fromRGB(255, 0, 0)
                    hl.OutlineColor = Color3.fromRGB(255, 255, 255)
                end
            end
        end
    end

    if following and targetName ~= "" then
        local targetP = nil
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and (p.Name:lower():sub(1, #targetName) == targetName:lower() or p.DisplayName:lower():sub(1, #targetName) == targetName:lower()) then
                targetP = p
                break
            end
        end
        if targetP and targetP.Character and targetP.Character:FindFirstChild("HumanoidRootPart") then
            local targetHrp = targetP.Character.HumanoidRootPart
            if flying and flyPlatform then
                flyPlatform.CFrame = targetHrp.CFrame * CFrame.new(0, -3.5, 3)
            else
                hrp.CFrame = targetHrp.CFrame * CFrame.new(0, 0, 3)
            end
        end
    end

    if pulling then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local targetHrp = p.Character.HumanoidRootPart
                targetHrp.CFrame = hrp.CFrame * CFrame.new(0, 0, -3)
                targetHrp.Velocity = Vector3.new(0, 0, 0)
            end
        end
    end
end)

LocalPlayer.CharacterRemoving:Connect(function()
    cleanupPlatform()
end)
