local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local LP = Players.LocalPlayer

local SG = Instance.new("ScreenGui", CoreGui)
SG.Name = "BC9_HITBOX"

local Main = Instance.new("Frame", SG)
Main.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
Main.Position = UDim2.new(0.5, -90, 0.5, -100)
Main.Size = UDim2.new(0, 180, 0, 215)
Main.Active = true
Main.Draggable = true
Main.ClipsDescendants = true

local Corner = Instance.new("UICorner", Main)
local Stroke = Instance.new("UIStroke", Main)
Stroke.Thickness = 2
Stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border

task.spawn(function()
    local h = 0
    while task.wait() do
        h = h + 0.005
        Stroke.Color = Color3.fromHSV(h % 1, 0.8, 1)
    end
end)

local Title = Instance.new("TextLabel", Main)
Title.Size = UDim2.new(1, 0, 0, 35)
Title.Text = "BC9 HITBOX UI"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Title.Font = Enum.Font.Code
Instance.new("UICorner", Title)

local Holder = Instance.new("Frame", Main)
Holder.BackgroundTransparency = 1
Holder.Size = UDim2.new(1, 0, 1, -35)
Holder.Position = UDim2.new(0, 0, 0, 35)

local Tgl = Instance.new("TextButton", Main)
Tgl.Size = UDim2.new(0, 20, 0, 20)
Tgl.Position = UDim2.new(1, -25, 0, 7)
Tgl.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
Tgl.Text = ""
Instance.new("UICorner", Tgl).CornerRadius = UDim.new(1, 0)

local open = true
Tgl.MouseButton1Click:Connect(function()
    open = not open
    Holder.Visible = open
    Main:TweenSize(open and UDim2.new(0, 180, 0, 215) or UDim2.new(0, 180, 0, 35), "Out", "Quart", 0.3, true)
    Tgl.BackgroundColor3 = open and Color3.fromRGB(200, 0, 0) or Color3.fromRGB(0, 200, 0)
end)

local function makeBtn(txt, pos, cb)
    local btn = Instance.new("TextButton", Holder)
    btn.Position = pos
    btn.Size = UDim2.new(0, 160, 0, 45)
    btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.SourceSansBold
    btn.Text = txt .. ": OFF"
    Instance.new("UICorner", btn)
    
    local on = false
    btn.MouseButton1Click:Connect(function()
        on = not on
        btn.Text = txt .. ": " .. (on and "ON" or "OFF")
        btn.BackgroundColor3 = on and Color3.fromRGB(0, 90, 180) or Color3.fromRGB(30, 30, 30)
        cb(on)
    end)
end

local Flags = {Body = false, Head = false, ESP = false}
makeBtn("BODY", UDim2.new(0, 10, 0, 10), function(v) Flags.Body = v end)
makeBtn("HEAD", UDim2.new(0, 10, 0, 65), function(v) Flags.Head = v end)
makeBtn("ESP",  UDim2.new(0, 10, 0, 120), function(v) Flags.ESP = v end)

RunService.RenderStepped:Connect(function()
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LP and p.Character then
            local char = p.Character
            local isEnemy = (p.Team ~= LP.Team or LP.Team == nil)
            local hum = char:FindFirstChild("Humanoid")
            local hrp = char:FindFirstChild("HumanoidRootPart")
            local head = char:FindFirstChild("Head")

            if hum and hum.Health > 0 and isEnemy then
                if Flags.ESP then
                    if not char:FindFirstChild("BC9_ESP") then
                        Instance.new("Highlight", char).Name = "BC9_ESP"
                    end
                else
                    if char:FindFirstChild("BC9_ESP") then char.BC9_ESP:Destroy() end
                end

                if hrp then
                    hrp.Size = Flags.Body and Vector3.new(20, 20, 20) or Vector3.new(2, 2, 1)
                    hrp.Transparency = Flags.Body and 0.75 or 1
                    hrp.CanCollide = false
                end

                if head then
                    head.Size = Flags.Head and Vector3.new(20, 20, 20) or Vector3.new(2, 1, 1)
                    head.Transparency = Flags.Head and 0.75 or 0
                    head.CanCollide = false
                end
            else
                if char:FindFirstChild("BC9_ESP") then char.BC9_ESP:Destroy() end
                if hrp then hrp.Size = Vector3.new(2, 2, 1) hrp.Transparency = 1 end
                if head then head.Size = Vector3.new(2, 1, 1) head.Transparency = 0 end
            end
        end
    end
end)
