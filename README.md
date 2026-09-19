-- ตรวจสอบว่ามี UI เก่าอยู่ไหม ถ้ามีให้ลบออกก่อน
if game.CoreGui:FindFirstChild("BlackScreenMenu") then
    game.CoreGui.BlackScreenMenu:Destroy()
end

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- สร้าง ScreenGui หลัก
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "BlackScreenMenu"
screenGui.ResetOnSpawn = false
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- ป้องกันไม่ให้โดนตรวจจับในบางตัว (ใส่ CoreGui ถ้าทำได้ หรือ PlayerGui)
pcall(function()
    screenGui.Parent = game.CoreGui
end)
if not screenGui.Parent then
    screenGui.Parent = playerGui
end

-- 1. สร้างเลเยอร์จอดำ (ให้อยู่ต่ำกว่าปุ่ม UI ของ Roblox แต่ทับเกมทั้งหมด)
local blackFrame = Instance.new("Frame")
blackFrame.Name = "BlackBackground"
blackFrame.Size = UDim2.new(1, 0, 1, 0)
blackFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
blackFrame.BackgroundTransparency = 1 -- เริ่มต้นแบบใส (ยังไม่ทำงาน)
blackFrame.BorderSizePixel = 0
blackFrame.ZIndex = 99999
blackFrame.Visible = false
blackFrame.Parent = screenGui

-- 2. สร้างปุ่มเมนูที่มุมขวาล่าง
local toggleButton = Instance.new("TextButton")
toggleButton.Name = "ToggleMenuButton"
toggleButton.Size = UDim2.new(0, 120, 0, 45)
-- ตำแหน่งมุมขวาล่าง
toggleButton.Position = UDim2.new(1, -140, 1, -65)
toggleButton.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.TextSize = 16
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.Text = "กดปิด/เปิด"
toggleButton.ZIndex = 100000
toggleButton.Parent = screenGui

-- ทำมุมปุ่มให้โค้งมนสวยงาม
local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0, 8)
uiCorner.Parent = toggleButton

-- สถานะการเปิด/ปิดจอดำ
local isBlack = false

local function toggleBlackScreen()
    isBlack = not isBlack
    if isBlack then
        blackFrame.Visible = true
        blackFrame.BackgroundTransparency = 0 -- ดำสนิท
        toggleButton.BackgroundColor3 = Color3.fromRGB(180, 40, 40) -- เปลี่ยนสีปุ่มเป็นแดงเมื่อเปิด
    else
        blackFrame.BackgroundTransparency = 1
        blackFrame.Visible = false
        toggleButton.BackgroundColor3 = Color3.fromRGB(35, 35, 35) -- กลับเป็นสีเดิม
    end
end

-- กดคลิกที่ปุ่มเพื่อสลับสถานะ
toggleButton.MouseButton1Click:Connect(toggleBlackScreen)

-- ตั้งค่าคีย์ลัดกดปุ่ม O เพื่อเปิด/ปิด
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.O then
        toggleBlackScreen()
    end
end)
