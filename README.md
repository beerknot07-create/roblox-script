local P, R, U, C = game:GetService("Players"), game:GetService("RunService"), game:GetService("UserInputService"), workspace.CurrentCamera
local L = P.LocalPlayer

-- สถานะฟังก์ชันทั้งหมด
local flying, espEnabled, speedEnabled, jumpEnabled, invisEnabled = false, false, false, false, false
local following, pulling = false, false
local targetName = ""
local speedVal, jumpVal = 50, 100
local flySpeed = 2 -- ค่าสัมประสิทธิ์ความเร็วแบบ CFrame (2 กำลังดี ควบคุมง่าย)
local currentCategory = "Main"
local savedParts = {}

-- สร้างหน้าต่าง UI หลัก
local sg = Instance.new("ScreenGui", L.PlayerGui)
sg.ResetOnSpawn = false

local f = Instance.new("Frame", sg)
f.Size, f.Position, f.BackgroundColor3, f.Active, f.Draggable = UDim2.new(0, 280, 0, 390), UDim2.new(0.5, -140, 0.3, -195), Color3.fromRGB(25, 25, 25), true, true
f.BorderSizePixel = 0

local corner = Instance.new("UICorner", f)
corner.CornerRadius = UDim.new(0, 8)

local tH = Instance.new("TextLabel", f)
tH.Size, tH.BackgroundColor3, tH.Text, tH.TextColor3, tH.Font, tH.TextSize = UDim2.new(1, -30, 0, 35), Color3.fromRGB(35, 35, 35), "  zxtata | เมนูหลัก", Color3.fromRGB(255, 255, 255), Enum.Font.SourceSansBold, 14
tH.BorderSizePixel = 0
local cornerH = Instance.new("UICorner", tH)
cornerH.CornerRadius = UDim.new(0, 8)

local mB = Instance.new("TextButton", f)
mB.Size, mB.Position, mB.BackgroundColor3, mB.Text, mB.TextColor3 = UDim2.new(0, 30, 0, 35), UDim2.new(1, -30, 0, 0), Color3.fromRGB(45, 45, 45), "-", Color3.fromRGB(255, 255, 255)
mB.BorderSizePixel = 0
local cornerB = Instance.new("UICorner", mB)
cornerB.CornerRadius = UDim.new(0, 8)

-- ปุ่มสลับหมวดหมู่
local tabMain = Instance.new("TextButton", f)
tabMain.Size, tabMain.Position, tabMain.BackgroundColor3, tabMain.Text, tabMain.TextColor3, tabMain.Font, tabMain.TextSize = UDim2.new(0.5, 0, 0, 30), UDim2.new(0, 0, 0, 35), Color3.fromRGB(55, 55, 55), "หมวดหมู่หลัก", Color3.fromRGB(100, 255, 100), Enum.Font.SourceSansBold, 13
tabMain.BorderSizePixel = 0

local tabTroll = Instance.new("TextButton", f)
tabTroll.Size, tabTroll.Position, tabTroll.BackgroundColor3, tabTroll.Text, tabTroll.TextColor3, tabTroll.Font, tabTroll.TextSize = UDim2.new(0.5, 0, 0, 30), UDim2.new(0.5, 0, 0, 35), Color3.fromRGB(40, 40, 40), "หมวดหมู่ป่วน", Color3.fromRGB(200, 200, 200), Enum.Font.SourceSansBold, 13
tabTroll.BorderSizePixel = 0

-- หน้าต่างเนื้อหา
local mainContainer = Instance.new("Frame", f)
mainContainer.Size, mainContainer.Position, mainContainer.BackgroundTransparency = UDim2.new(1, 0, 1, -65), UDim2.new(0, 0, 0, 65), true

local trollContainer = Instance.new("Frame", f)
trollContainer.Size, trollContainer.Position, trollContainer.BackgroundTransparency = UDim2.new(1, 0, 1, -65), UDim2.new(0, 0, 0, 65), true
trollContainer.Visible = false

local function createBtn(parent, t, y)
    local b = Instance.new("TextButton", parent)
    b.Size, b.Position, b.BackgroundColor3, b.Text, b.TextColor3, b.Font, b.TextSize = UDim2.new(0, 240, 0, 35), UDim2.new(0, 20, 0, y), Color3.fromRGB(45, 45, 45), t, Color3.fromRGB(255, 100, 100), Enum.Font.SourceSansBold, 14
    b.BorderSizePixel = 0
    local c = Instance.new("UICorner", b)
    c.CornerRadius = UDim.new(0, 6)
    return b
end

-- ปุ่มหมวดหมู่หลัก
local flyBtn = createBtn(mainContainer, "ระบบ บินอิสระ: ปิดอยู่", 10)

local flySpeedBox = Instance.new("TextBox", mainContainer)
flySpeedBox.Size, flySpeedBox.Position, flySpeedBox.BackgroundColor3, flySpeedBox.PlaceholderText, flySpeedBox.Text, flySpeedBox.TextColor3, flySpeedBox.Font, flySpeedBox.TextSize = UDim2.new(0, 240, 0, 30), UDim2.new(0, 20, 0, 55), Color3.fromRGB(35, 35, 35), "⚡ ความเร็วบิน (เริ่มต้น 50)...", "50", Color3.fromRGB(255, 255, 255), Enum.Font.SourceSans, 13
flySpeedBox.BorderSizePixel = 0
local cornerFS = Instance.new("UICorner", flySpeedBox)
cornerFS.CornerRadius = UDim.new(0, 6)
flySpeedBox.FocusLost:Connect(function()
    local num = tonumber(flySpeedBox.Text)
    if num then flySpeed = num / 25 else flySpeed = 2 flySpeedBox.Text = "50" end
end)

local espBtn = createBtn(mainContainer, "ระบบ มองทะลุ (ESP): ปิดอยู่", 100)
local speedBtn = createBtn(mainContainer, "ระบบ วิ่งเร็ว: ปิดอยู่", 145)
local jumpBtn = createBtn(mainContainer, "ระบบ กระโดดสูง: ปิดอยู่", 190)
local invisBtn = createBtn(mainContainer, "ระบบ ล่องหน: ปิดอยู่", 235)

-- ปุ่มหมวดหมู่ป่วน
local followBtn = createBtn(trollContainer, "ระบบ วาร์ปตามติดตัว: ปิดอยู่", 15)

local nameBox = Instance.new("TextBox", trollContainer)
nameBox.Size, nameBox.Position, nameBox.BackgroundColor3, nameBox.PlaceholderText, nameBox.Text, nameBox.TextColor3, nameBox.Font, nameBox.TextSize = UDim2.new(0, 240, 0, 30), UDim2.new(0, 20, 0, 65), Color3.fromRGB(35, 35, 35), "👉 พิมพ์ชื่อคนที่จะตามตรงนี้...", "", Color3.fromRGB(255, 255, 255), Enum.Font.SourceSans, 13
nameBox.BorderSizePixel = 0
local cornerN = Instance.new("UICorner", nameBox)
cornerN.CornerRadius = UDim.new(0, 6)
nameBox.FocusLost:Connect(function() targetName = nameBox.Text end)

local pullBtn = createBtn(trollContainer, "ระบบ ดึงคนทั้งเซิร์ฟ: ปิดอยู่", 120)

-- ระบบสลับแท็บ
tabMain.MouseButton1Click:Connect(function()
    currentCategory = "Main"
    mainContainer.Visible = true
    trollContainer.Visible = false
    tabMain.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
    tabMain.TextColor3 = Color3.fromRGB(100, 255, 100)
    tabTroll.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    tabTroll.TextColor3 = Color3.fromRGB(200, 200, 200)
    tH.Text = "  zxtata | เมนูหลัก"
end)

tabTroll.MouseButton1Click:Connect(function()
    currentCategory = "Troll"
    mainContainer.Visible = false
    trollContainer.Visible = true
    tabTroll.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
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
    f.Size = min and UDim2.new(0, 280, 0, 35) or UDim2.new(0, 280, 0, 390)
    mB.Text = min and "+" or "-"
end)

-- ปุ่มเปิด/ปิดบิน
flyBtn.MouseButton1Click:Connect(function()
    flying = not flying
    flyBtn.Text = flying and "ระบบ บินอิสระ: เปิดใช้งาน" or "ระบบ บินอิสระ: ปิดอยู่"
    flyBtn.TextColor3 = flying and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    
    local c = L.Character
    local h = c and c:FindFirstChild("Humanoid")
    local anim = c and c:FindFirstChild("Animate")
    local r = c and c:FindFirstChild("HumanoidRootPart")
    
    if h then
        if flying then
            if anim then anim.Enabled = false end
            for _, tr in ipairs(h:GetPlayingAnimationTracks()) do tr:Stop() end
        else
            if anim then anim.Enabled = true end
            if r then r.Velocity = Vector3.new(0,0,0) end
        end
    end
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

invisBtn.MouseButton1Click:Connect(function()
    invisEnabled = not invisEnabled
    invisBtn.Text = invisEnabled and "ระบบ ล่องหน: เปิดใช้งาน" or "ระบบ ล่องหน: ปิดอยู่"
    invisBtn.TextColor3 = invisEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
    
    local char = L.Character
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

-- ลูปการทำงานเบื้องหลัง
R.RenderStepped:Connect(function()
    local char = L.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") or not char:FindFirstChild("Humanoid") then return end
    local hrp = char.HumanoidRootPart
    local hum = char.Humanoid

    if speedEnabled then hum.WalkSpeed = speedVal else hum.WalkSpeed = 16 end
    if jumpEnabled then hum.JumpPower = jumpVal else hum.JumpPower = 50 end

    -- [ระบบบินอมตะด้วยการเปลี่ยนพิกัด CFrame แบบ Direct]
    if flying then
        -- บังคับตัดแรงโน้มถ่วงไม่ให้ร่วงดิ่งพื้น
        hrp.Velocity = Vector3.new(0, 0, 0)

        -- ล็อกหน้าตัวละครให้หันตรงตามมุมกล้องในแนวราบเสมอ 
        local lookDir = C.CFrame.LookVector
        local flatLookAt = Vector3.new(lookDir.X, 0, lookDir.Z).Unit
        local nextCFrame = CFrame.new(hrp.Position, hrp.Position + flatLookAt)

        -- เช็คการป้อนค่าเดินขึ้นลงเสริม (เช่นคีย์บอร์ด Space / Ctrl)
        local verticalMove = 0
        if U:IsKeyDown(Enum.KeyCode.Space) then verticalMove = 1
        elseif U:IsKeyDown(Enum.KeyCode.LeftControl) then verticalMove = -1 end

        -- เช็คการดันจอยสติ๊กบนมือถือ (MoveDirection จะจับพิกัดได้ตลอดเวลา)
        if hum.MoveDirection.Magnitude > 0 or verticalMove ~= 0 then
            local moveVector = hum.MoveDirection * flySpeed
            local verticalVector = Vector3.new(0, verticalMove * flySpeed, 0)
            -- เลื่อนพิกัดของตัวละครตรงๆ ทุกเฟรมตามจอยสติ๊ก
            hrp.CFrame = nextCFrame + moveVector + verticalVector
        else
            -- ปล่อยจอยสติ๊กปุ๊บ ล็อกตัวนิ่งค้างกลางอากาศ หันหน้าตามกล้องทันที
            hrp.CFrame = nextCFrame
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
