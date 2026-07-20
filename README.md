local P, R, U, C = game:GetService("Players"), game:GetService("RunService"), game:GetService("UserInputService"), workspace.CurrentCamera
local L = P.LocalPlayer

-- สถานะฟังก์ชันทั้งหมด
local flying, espEnabled, speedEnabled, jumpEnabled = false, false, false, false
local flinging, following, pulling = false, false, false
local targetName = ""
local speedVal, jumpVal = 50, 100
local currentCategory = "Main" -- ค่าเริ่มต้น: หมวดหมู่หลัก

-- สร้างหน้าต่าง UI หลัก
local sg = Instance.new("ScreenGui", L.PlayerGui)
sg.ResetOnSpawn = false

local f = Instance.new("Frame", sg)
f.Size, f.Position, f.BackgroundColor3, f.Active, f.Draggable = UDim2.new(0, 280, 0, 310), UDim2.new(0.5, -140, 0.3, -155), Color3.fromRGB(20, 20, 20), true, true

local tH = Instance.new("TextLabel", f)
tH.Size, tH.BackgroundColor3, tH.Text, tH.TextColor3, tH.Font, tH.TextSize = UDim2.new(1, -30, 0, 30), Color3.fromRGB(30, 30, 30), "  zxtata | เมนูหลัก", Color3.fromRGB(255, 255, 255), Enum.Font.SourceSansBold, 14

local mB = Instance.new("TextButton", f)
mB.Size, mB.Position, mB.BackgroundColor3, mB.Text, mB.TextColor3 = UDim2.new(0, 30, 0, 30), UDim2.new(1, -30, 0, 0), Color3.fromRGB(40, 40, 40), "-", Color3.fromRGB(255, 255, 255)

-- ปุ่มสลับหมวดหมู่ (Tab Switcher)
local tabMain = Instance.new("TextButton", f)
tabMain.Size, tabMain.Position, tabMain.BackgroundColor3, tabMain.Text, tabMain.TextColor3, tabMain.Font, tabMain.TextSize = UDim2.new(0.5, 0, 0, 30), UDim2.new(0, 0, 0, 30), Color3.fromRGB(60, 60, 60), "หมวดหมู่หลัก", Color3.fromRGB(100, 255, 100), Enum.Font.SourceSansBold, 13

local tabTroll = Instance.new("TextButton", f)
tabTroll.Size, tabTroll.Position, tabTroll.BackgroundColor3, tabTroll.Text, tabTroll.TextColor3, tabTroll.Font, tabTroll.TextSize = UDim2.new(0.5, 0, 0, 30), UDim2.new(0.5, 0, 0, 30), Color3.fromRGB(40, 40, 40), "หมวดหมู่ป่วน", Color3.fromRGB(200, 200, 200), Enum.Font.SourceSansBold, 13

-- หน้าต่างเนื้อหาของแต่ละหมวด
local mainContainer = Instance.new("Frame", f)
mainContainer.Size, mainContainer.Position, mainContainer.BackgroundTransparency = UDim2.new(1, 0, 1, -60), UDim2.new(0, 0, 0, 60), true

local trollContainer = Instance.new("Frame", f)
trollContainer.Size, trollContainer.Position, trollContainer.BackgroundTransparency = UDim2.new(1, 0, 1, -60), UDim2.new(0, 0, 0, 60), true
trollContainer.Visible = false

-- ฟังก์ชันสร้างปุ่มในหมวด
local function createBtn(parent, t, y)
    local b = Instance.new("TextButton", parent)
    b.Size, b.Position, b.BackgroundColor3, b.Text, b.TextColor3, b.Font, b.TextSize = UDim2.new(0, 240, 0, 35), UDim2.new(0, 20, 0, y), Color3.fromRGB(45, 45, 45), t, Color3.fromRGB(255, 100, 100), Enum.Font.SourceSansBold, 14
    return b
end

-- ปุ่มหมวดหมู่หลัก
local flyBtn = createBtn(mainContainer, "ระบบ บินอิสระ: ปิดอยู่", 10)
local espBtn = createBtn(mainContainer, "ระบบ มองทะลุ (ESP): ปิดอยู่", 55)
local speedBtn = createBtn(mainContainer, "ระบบ วิ่งเร็ว: ปิดอยู่", 100)
local jumpBtn = createBtn(mainContainer, "ระบบ กระโดดสูง: ปิดอยู่", 145)

-- ปุ่มหมวดหมู่ป่วน
local flingBtn = createBtn(trollContainer, "ระบบ ชนคนกระเด็น: ปิดอยู่", 10)
local followBtn = createBtn(trollContainer, "ระบบ วาร์ปตามติดตัว: ปิดอยู่", 55)

local nameBox = Instance.new("TextBox", trollContainer)
nameBox.Size, nameBox.Position, nameBox.BackgroundColor3, nameBox.PlaceholderText, nameBox.Text, nameBox.TextColor3, nameBox.Font, nameBox.TextSize = UDim2.new(0, 240, 0, 30), UDim2.new(0, 20, 0, 100), Color3.fromRGB(35, 35, 35), "👉 พิมพ์ชื่อคนที่จะตามตรงนี้...", "", Color3.fromRGB(255, 255, 255), Enum.Font.SourceSans, 13
nameBox.FocusLost:Connect(function() targetName = nameBox.Text end)

local pullBtn = createBtn(trollContainer, "ระบบ ดึงคนทั้งเซิร์ฟ: ปิดอยู่", 145)

-- ระบบสลับแท็บ
tabMain.MouseButton1Click:Connect(function()
    currentCategory = "Main"
    mainContainer.Visible = true
    trollContainer.Visible = false
    tabMain.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    tabMain.TextColor3 = Color3.fromRGB(100, 255, 100)
    tabTroll.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    tabTroll.TextColor3 = Color3.fromRGB(200, 200, 200)
    tH.Text = "  zxtata | เมนูหลัก"
end)

tabTroll.MouseButton1Click:Connect(function()
    currentCategory = "Troll"
    mainContainer.Visible = false
    trollContainer.Visible = true
    tabTroll.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    tabTroll.TextColor3 = Color3.fromRGB(100, 255, 100)
    tabMain.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    tabMain.TextColor3 = Color3.fromRGB(200, 200, 200)
    tH.Text = "  zxtata | หมวดหมู่ป่วน"
end)

-- ย่อ/ขยายหน้าต่าง
local min = false
mB.MouseButton1Click:Connect(function()
    min = not min
    mainContainer.Visible = not min and (currentCategory == "Main")
    trollContainer.Visible = not min and (currentCategory == "Troll")
    tabMain.Visible = not min
    tabTroll.Visible = not min
    f.Size = min and UDim2.new(0, 280, 0, 30) or UDim2.new(0, 280, 0, 310)
    mB.Text = min and "+" or "-"
end)

-- ปุ่ม Insert ซ่อน/เปิดเมนู
U.InputBegan:Connect(function(i, g)
    if not g and i.KeyCode == Enum.KeyCode.Insert then
        f.Visible = not f.Visible
    end
end)

-- ควบคุมปุ่มทำงาน
flyBtn.MouseButton1Click:Connect(function()
    flying = not flying
    flyBtn.Text = flying and "ระบบ บินอิสระ: เปิดใช้งาน" or "ระบบ บินอิสระ: ปิดอยู่"
    flyBtn.TextColor3 = flying and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

espBtn.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    espBtn.Text = espEnabled and "ระบบ มองทะลุ (ESP): เปิดใช้งาน" or "ระบบ มองทะลุ (ESP): ปิดอยู่"
    espBtn.TextColor3 = espEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    if not espEnabled then
        for _, p in ipairs(P:GetPlayers()) do
            if p.Character and p.Character:FindFirstChild("Highlight") then p.Character.Highlight:Destroy() end
        end
    end
end)

speedBtn.MouseButton1Click:Connect(function()
    speedEnabled = not speedEnabled
    speedBtn.Text = speedEnabled and "ระบบ วิ่งเร็ว: เปิดใช้งาน" or "ระบบ วิ่งเร็ว: ปิดอยู่"
    speedBtn.TextColor3 = speedEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

jumpBtn.MouseButton1Click:Connect(function()
    jumpEnabled = not jumpEnabled
    jumpBtn.Text = jumpEnabled and "ระบบ กระโดดสูง: เปิดใช้งาน" or "ระบบ กระโดดสูง: ปิดอยู่"
    jumpBtn.TextColor3 = jumpEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

flingBtn.MouseButton1Click:Connect(function()
    flinging = not flinging
    flingBtn.Text = flinging and "ระบบ ชนคนกระเด็น: เปิดใช้งาน" or "ระบบ ชนคนกระเด็น: ปิดอยู่"
    flingBtn.TextColor3 = flinging and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

followBtn.MouseButton1Click:Connect(function()
    following = not following
    followBtn.Text = following and "ระบบ วาร์ปตามติดตัว: เปิดใช้งาน" or "ระบบ วาร์ปตามติดตัว: ปิดอยู่"
    followBtn.TextColor3 = following and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

pullBtn.MouseButton1Click:Connect(function()
    pulling = not pulling
    pullBtn.Text = pulling and "ระบบ ดึงคนทั้งเซิร์ฟ: เปิดใช้งาน" or "ระบบ ดึงคนทั้งเซิร์ฟ: ปิดอยู่"
    pullBtn.TextColor3 = pulling and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end)

-- ลูปการทำงานเบื้องหลังทั้งหมด
R.RenderStepped:Connect(function()
    local char = L.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") or not char:FindFirstChild("Humanoid") then return end
    local hrp = char.HumanoidRootPart
    local hum = char.Humanoid

    if speedEnabled then hum.WalkSpeed = speedVal else hum.WalkSpeed = 16 end
    if jumpEnabled then hum.JumpPower = jumpVal else hum.JumpPower = 50 end

    if flying then
        local cam = workspace.CurrentCamera
        hrp.Velocity = Vector3.new(0, 0, 0)
        local moveDir = hum.MoveDirection
        if moveDir.Magnitude > 0 then
            hrp.CFrame = hrp.CFrame + (cam.CFrame.LookVector * moveDir.Z + cam.CFrame.RightVector * moveDir.X) * 2
        end
    end

    if espEnabled then
        for _, p in ipairs(P:GetPlayers()) do
            if p ~= L and p.Character then
                if not p.Character:FindFirstChild("Highlight") then
                    local hl = Instance.new("Highlight", p.Character)
                    hl.FillColor = Color3.fromRGB(255, 0, 0)
                    hl.OutlineColor = Color3.fromRGB(255, 255, 255)
                end
            end
        end
    end

    if flinging then
        hrp.Velocity = Vector3.new(30000, 30000, 30000)
        hrp.RotVelocity = Vector3.new(50000, 50000, 50000)
    end

    if following and targetName ~= "" then
        local targetP = nil
        for _, p in ipairs(P:GetPlayers()) do
            if p ~= L and (p.Name:lower():sub(1, #targetName) == targetName:lower() or p.DisplayName:lower():sub(1, #targetName) == targetName:lower()) then
                targetP = p
                break
            end
        end
        if targetP and targetP.Character and targetP.Character:FindFirstChild("HumanoidRootPart") then
            local targetHrp = targetP.Character.HumanoidRootPart
            hrp.CFrame = targetHrp.CFrame * CFrame.new(0, 0, 3)
            hrp.Velocity = Vector3.new(0, 0, 0)
        end
    end

    if pulling then
        for _, p in ipairs(P:GetPlayers()) do
            if p ~= L and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local targetHrp = p.Character.HumanoidRootPart
                targetHrp.CFrame = hrp.CFrame * CFrame.new(0, 0, -3)
                targetHrp.Velocity = Vector3.new(0, 0, 0)
            end
        end
    end
end)