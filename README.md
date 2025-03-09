
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local rootPart = character:WaitForChild("HumanoidRootPart")

local flyButton = script.Parent:WaitForChild("FlyButton") -- Altere conforme necessário
local speedBox = script.Parent:WaitForChild("SpeedBox") -- Altere conforme necessário

local flying = false
local speed = 50
local bodyVelocity
local bodyGyro

-- Funções para ativar/desativar o Fly
local function startFlying()
    flying = true
    bodyVelocity = Instance.new("BodyVelocity", rootPart)
    bodyVelocity.MaxForce = Vector3.new(1e4, 1e4, 1e4)
    bodyGyro = Instance.new("BodyGyro", rootPart)
    bodyGyro.MaxTorque = Vector3.new(1e8, 1e8, 1e8)
end

local function stopFlying()
    flying = false
    if bodyVelocity then bodyVelocity:Destroy() end
    if bodyGyro then bodyGyro:Destroy() end
end

-- Conexões para controles
flyButton.MouseButton1Click:Connect(function()
    if flying then
        stopFlying()
        flyButton.Text = "Ativar Fly"
    else
        startFlying()
        flyButton.Text = "Desativar Fly"
    end
end)

speedBox.FocusLost:Connect(function()
    local inputSpeed = tonumber(speedBox.Text)
    if inputSpeed and inputSpeed >= 0 and inputSpeed <= 100 then
        speed = inputSpeed
    else
        speedBox.Text = tostring(speed) -- Reseta se inválido
    end
end)

-- Atualizar voo
game:GetService("RunService").RenderStepped:Connect(function()
    if flying and bodyVelocity then
        bodyVelocity.Velocity = workspace.CurrentCamera.CFrame.LookVector * speed
        bodyGyro.CFrame = workspace.CurrentCamera.CFrame
    end
end)
