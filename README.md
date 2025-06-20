--[[
✅ Script para Arceus X e similares
🛡️ Botão neutro, seguro e móvel
📌 Teleporta para player com 0 de HP por 2s e volta
🧱 Tool que remove paredes ao clicar
]]

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- 🛡️ Anti-Detect básico
pcall(function()
    if setfflag then
        setfflag("AbuseReportScreenshot", "False")
        setfflag("AbuseReportScreenshotPercentage", "0")
    end
end)

-- 🖱️ Criar botão quadrado seguro (visual de botão de erro padrão)
local CoreGui = game:GetService("CoreGui")
local gui = Instance.new("ScreenGui", CoreGui)
gui.Name = "SafeNeutralGUI"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true

local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 100, 0, 40)
button.Position = UDim2.new(0.5, -50, 0.5, -20)
button.Text = ""
button.BackgroundColor3 = Color3.fromRGB(120, 120, 120)
button.BorderSizePixel = 2
button.BorderColor3 = Color3.fromRGB(0, 150, 255) -- azul claro estilo erro do sistema
button.AutoButtonColor = false
button.Draggable = true
button.Active = true
button.Parent = gui

-- 👣 Teleporte rápido para jogador morto
local busy = false
local lastPos

local function getDeadPlayer()
    local closest, dist = nil, math.huge
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Humanoid") and p.Character:FindFirstChild("HumanoidRootPart") then
            if p.Character.Humanoid.Health <= 0 then
                local d = (LocalPlayer.Character.HumanoidRootPart.Position - p.Character.HumanoidRootPart.Position).Magnitude
                if d < dist then
                    dist = d
                    closest = p.Character.HumanoidRootPart
                end
            end
        end
    end
    return closest
end

button.MouseButton1Click:Connect(function()
    if busy then return end
    busy = true

    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then
        busy = false
        return
    end

    lastPos = char.HumanoidRootPart.CFrame
    local target = getDeadPlayer()
    if target then
        char.HumanoidRootPart.CFrame = target.CFrame + Vector3.new(0, 2, 0)
        wait(2)
        char.HumanoidRootPart.CFrame = lastPos
    end
    busy = false
end)

-- 🛠️ Tool na Backpack que destrói obstáculos
local tool = Instance.new("Tool")
tool.Name = "WallBreaker"
tool.RequiresHandle = false

tool.Activated:Connect(function()
    local target = Mouse.Target
    if target and target:IsA("BasePart") and target.Locked == false then
        target:Destroy()
    end
end)

tool.Parent = LocalPlayer:FindFirstChild("Backpack") or LocalPlayer:WaitForChild("Backpack")

tool.AncestryChanged:Connect(function()
    if not tool:IsDescendantOf(LocalPlayer) then
        wait(1)
        tool.Parent = LocalPlayer:FindFirstChild("Backpack")
    end
end)
