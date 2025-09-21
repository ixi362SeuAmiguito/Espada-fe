local plr = game.Players.LocalPlayer
local tool = Instance.new("Tool")
tool.Name = "StarSword"
tool.RequiresHandle = true
tool.CanBeDropped = true
tool.Parent = plr.Backpack

local handle = Instance.new("Part")
handle.Name = "Handle"
handle.Size = Vector3.new(1, 0.8, 6)
handle.Anchored = false
handle.CanCollide = false
handle.Color = Color3.fromRGB(255, 255, 255)
handle.Parent = tool

local mesh = Instance.new("SpecialMesh")
mesh.MeshId = "rbxasset://fonts/sword.mesh"
mesh.TextureId = "rbxasset://textures/SwordTexture.png"
mesh.Scale = Vector3.new(1.5, 1.5, 1.5)
mesh.Parent = handle

local particle = Instance.new("ParticleEmitter")
particle.Texture = "rbxassetid://241594314"
particle.Rate = 50
particle.Lifetime = NumberRange.new(0.5,1)
particle.Speed = NumberRange.new(2,4)
particle.Rotation = NumberRange.new(0,360)
particle.RotSpeed = NumberRange.new(50,150)
particle.Size = NumberSequence.new(1)
particle.Color = ColorSequence.new(Color3.fromRGB(255,255,0), Color3.fromRGB(255,0,255))
particle.Parent = handle

tool.GripForward = Vector3.new(0, -1, 0)
tool.GripRight   = Vector3.new(1, 0, 0)
tool.GripUp      = Vector3.new(0, 0, 1)
tool.GripPos     = Vector3.new(0, 0, -2)

local humanoid = plr.Character:WaitForChild("Humanoid")
local R15 = humanoid.RigType == Enum.HumanoidRigType.R15

local Animations = {
    Equip = R15 and "rbxassetid://94160581" or "rbxassetid://94160581",
    Unequip = R15 and "rbxassetid://94095929" or "rbxassetid://94095929",
    Slash1 = R15 and "rbxassetid://94161088" or "rbxassetid://94161088",
    Slash2 = R15 and "rbxassetid://94161333" or "rbxassetid://94161333",
    Lunge = R15 and "rbxassetid://94160738" or "rbxassetid://94160738",
    Idle = R15 and "rbxassetid://94108418" or "rbxassetid://94108418"
}

local LoadedAnims = {}
for name,id in pairs(Animations) do
    local anim = Instance.new("Animation")
    anim.AnimationId = id
    LoadedAnims[name] = humanoid:LoadAnimation(anim)
end

local attackState = 1
local canAttack = true

local gui = Instance.new("ScreenGui", plr:WaitForChild("PlayerGui"))
local msg = Instance.new("TextLabel", gui)
msg.Size = UDim2.new(0,400,0,50)
msg.Position = UDim2.new(0.5,-200,0.1,0)
msg.Text = "Content Creator ixi362"
msg.TextColor3 = Color3.new(1,1,1)
msg.BackgroundTransparency = 1
msg.Font = Enum.Font.SourceSansBold
msg.TextScaled = true
msg.TextTransparency = 1
for i=0,1,0.05 do
    msg.TextTransparency = 1-i
    wait(0.03)
end
wait(2)
for i=0,1,0.05 do
    msg.TextTransparency = i
    wait(0.03)
end
msg:Destroy()

local playerParticles = Instance.new("ParticleEmitter")
playerParticles.Texture = "rbxassetid://241594314"
playerParticles.Rate = 30
playerParticles.Lifetime = NumberRange.new(0.5,1)
playerParticles.Speed = NumberRange.new(1,2)
playerParticles.Rotation = NumberRange.new(0,360)
playerParticles.RotSpeed = NumberRange.new(50,100)
playerParticles.Size = NumberSequence.new(1)
playerParticles.Color = ColorSequence.new(Color3.fromRGB(255,255,0), Color3.fromRGB(255,0,255))
local root = plr.Character:FindFirstChild("HumanoidRootPart") or plr.Character:WaitForChild("Torso")
playerParticles.Parent = root

tool.Equipped:Connect(function()
    LoadedAnims.Equip:Play()
    LoadedAnims.Equip.Stopped:Connect(function()
        LoadedAnims.Idle:Play()
    end)

    tool.Activated:Connect(function()
        if not canAttack then return end
        canAttack = false
        LoadedAnims.Idle:Stop()

        if attackState == 1 then
            LoadedAnims.Slash1:Play()
        elseif attackState == 2 then
            LoadedAnims.Slash2:Play()
        else
            LoadedAnims.Lunge:Play()
        end

        attackState = attackState % 3 + 1
        wait(0.5)
        canAttack = true
        LoadedAnims.Idle:Play()
    end)
end)

tool.Unequipped:Connect(function()
    LoadedAnims.Unequip:Play()
    LoadedAnims.Idle:Stop()
    playerParticles:Destroy()
end)
