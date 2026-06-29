-- Name: FPSBoostGUI
-- Location: StarterGui
-- Type: LocalScript

local Lighting = game:GetService("Lighting")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

-- Create GUI Elements
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "FPSBoostMenu"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game:GetService("Players").LocalPlayer:WaitForChild("PlayerGui")

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 250, 0, 300)
MainFrame.Position = UDim2.new(0.5, -125, 0.5, -150)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = MainFrame

local UIStroke = Instance.new("UIStroke")
UIStroke.Color = Color3.fromRGB(60, 60, 75)
UIStroke.Thickness = 2
UIStroke.Parent = MainFrame

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundTransparency = 1
Title.Text = "FPS BOOST V1"
Title.TextColor3 = Color3.fromRGB(240, 240, 245)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18
Title.Parent = MainFrame

local Container = Instance.new("ScrollingFrame")
Container.Size = UDim2.new(1, -20, 1, -60)
Container.Position = UDim2.new(0, 10, 0, 50)
Container.BackgroundTransparency = 1
Container.ScrollBarThickness = 2
Container.CanvasSize = UDim2.new(0, 0, 1.2, 0)
Container.Parent = MainFrame

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Padding = UDim.new(0, 8)
UIListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
UIListLayout.Parent = Container

-- Button Builder Function
local function createToggleButton(text, callback)
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(1, -10, 0, 40)
    Button.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    Button.Text = text
    Button.TextColor3 = Color3.fromRGB(200, 200, 210)
    Button.Font = Enum.Font.GothamMedium
    Button.TextSize = 14
    Button.AutoButtonColor = false
    Button.Parent = Container

    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 6)
    btnCorner.Parent = Button

    local toggled = false
    Button.MouseButton1Click:Connect(function()
        toggled = not toggled
        if toggled then
            Button.BackgroundColor3 = Color3.fromRGB(90, 130, 255)
            Button.TextColor3 = Color3.fromRGB(255, 255, 255)
        else
            Button.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
            Button.TextColor3 = Color3.fromRGB(200, 200, 210)
        end
        callback(toggled)
    end)
    
    return Button
end

-- Optimization Logic
local function optimizeLighting(state)
    if state then
        Lighting.GlobalShadows = false
        Lighting.FogEnd = 9e9
        for _, v in pairs(Lighting:GetChildren()) do
            if v:IsA("PostEffect") or v:IsA("Atmosphere") or v:IsA("Clouds") then
                v.Parent = game:GetService("TestService") -- Temporarily hide
            end
        end
    else
        Lighting.GlobalShadows = true
        for _, v in pairs(game:GetService("TestService"):GetChildren()) do
            v.Parent = Lighting
        end
    end
end

local function removeTextures(state)
    -- This sets all materials to SmoothPlastic locally to save VRAM
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("BasePart") then
            if state then
                if not v:FindFirstChild("OriginalMat") then
                    local val = Instance.new("StringValue")
                    val.Name = "OriginalMat"
                    val.Value = v.Material.Name
                    val.Parent = v
                end
                v.Material = Enum.Material.SmoothPlastic
            else
                local val = v:FindFirstChild("OriginalMat")
                if val then
                    v.Material = Enum.Material[val.Value]
                    val:Destroy()
                end
            end
        elseif v:IsA("Decal") or v:IsA("Texture") then
            v.Transparency = state and 1 or 0
        end
    end
end

local function toggleParticles(state)
    for _, v in pairs(workspace:
    
