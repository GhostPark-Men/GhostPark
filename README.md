-- GhostPark Script

local player = game.Players.LocalPlayer
local UIS = game:GetService("UserInputService")
local UserGui = player:WaitForChild("PlayerGui")

-- Criar GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GhostParkGUI"
screenGui.Parent = UserGui

local floatingFrame = Instance.new("Frame")
floatingFrame.Size = UDim2.new(0, 200, 0, 100)
floatingFrame.Position = UDim2.new(0.5, -100, 0.5, -50)
floatingFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
floatingFrame.Parent = screenGui

local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(1, 0, 0, 50)
titleLabel.Text = "GhostPark Universal"
titleLabel.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
titleLabel.TextColor3 = Color3.new(1, 1, 1)
titleLabel.Parent = floatingFrame

local minimizeButton = Instance.new("TextButton")
minimizeButton.Size = UDim2.new(0, 25, 0, 25)
minimizeButton.Position = UDim2.new(1, -30, 0, 0)
minimizeButton.Text = "-"
minimizeButton.Parent = floatingFrame

local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 25, 0, 25)
closeButton.Position = UDim2.new(1, -60, 0, 0)
closeButton.Text = "X"
closeButton.Parent = floatingFrame

local flyToggle = Instance.new("TextButton")
flyToggle.Size = UDim2.new(0, 50, 0, 25)
flyToggle.Position = UDim2.new(0, 10, 1, 10)
flyToggle.Text = "Fly"
flyToggle.Parent = floatingFrame

-- Variáveis de controle
local flying = false
local bodyVelocity

-- Funções
function toggleMinimize()
    floatingFrame.Visible = not floatingFrame.Visible
end

function closeGui()
    screenGui:Destroy()
end

function toggleFly()
    if flying then
        flying = false
        if bodyVelocity then
            bodyVelocity:Destroy()
            bodyVelocity = nil
        end
    else
        flying = true
        local character = player.Character or player.CharacterAdded:Wait()
        local rootPart = character:WaitForChild("HumanoidRootPart")
        bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.MaxForce = Vector3.new(4000, 4000, 4000)
        bodyVelocity.Parent = rootPart

        coroutine.wrap(function()
            while flying and bodyVelocity and rootPart and rootPart.Parent do
                local moveVector = Vector3.new(0, 0, 0)
                if UIS:IsKeyDown(Enum.KeyCode.W) then moveVector = moveVector + Vector3.new(0, 0, -1) end
                if UIS:IsKeyDown(Enum.KeyCode.S) then moveVector = moveVector + Vector3.new(0, 0, 1) end
                if UIS:IsKeyDown(Enum.KeyCode.A) then moveVector = moveVector + Vector3.new(-1, 0, 0) end
                if UIS:IsKeyDown(Enum.KeyCode.D) then moveVector = moveVector + Vector3.new(1, 0, 0) end
                if UIS:IsKeyDown(Enum.KeyCode.Space) then moveVector = moveVector + Vector3.new(0, 1, 0) end
                if UIS:IsKeyDown(Enum.KeyCode.LeftShift) then moveVector = moveVector + Vector3.new(0, -1, 0) end
                bodyVelocity.Velocity = moveVector.Unit * 50
                if moveVector.Magnitude == 0 then
                    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
                end
                wait()
            end
        end)()
    end
end

-- Eventos
minimizeButton.MouseButton1Click:Connect(toggleMinimize)
closeButton.MouseButton1Click:Connect(closeGui)
flyToggle.MouseButton1Click:Connect(toggleFly)

-- Movimentação do floating
local dragging = false
local dragStart
local startPos

floatingFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = floatingFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

floatingFrame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        input.Changed:Connect(function()
            if dragging then
                local delta = input.Position - dragStart
                floatingFrame.Position = startPos + UDim2.new(0, delta.X, 0, delta.Y)
            end
        end)
    end
end)
