local userInputService = game:GetService("UserInputService")
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local TextChatService = game:GetService("TextChatService")
local tweenService = game:GetService("TweenService")

-- Função para enviar a mensagem para o chat
local function sendChatMessage(message)
    local chatChannel = TextChatService.TextChannels.RBXGeneral
    chatChannel:SendAsync(message)
end

-- Função para criar a bola azul que atrai jogadores
local function createBlueBall()
    local ball = Instance.new("Part")
    ball.Size = Vector3.new(5, 5, 5)
    ball.Shape = Enum.PartType.Ball
    ball.Position = character.HumanoidRootPart.Position + character.HumanoidRootPart.CFrame.LookVector * 5
    ball.Anchored = true
    ball.BrickColor = BrickColor.new("Bright blue")
    ball.Material = Enum.Material.Neon
    ball.CanCollide = false
    ball.Parent = workspace

    local players = game.Players:GetPlayers()
    for _, otherPlayer in pairs(players) do
        if otherPlayer ~= player then
            local otherCharacter = otherPlayer.Character
            if otherCharacter and otherCharacter:FindFirstChild("HumanoidRootPart") then
                local distance = (otherCharacter.HumanoidRootPart.Position - ball.Position).Magnitude
                local direction = (ball.Position - otherCharacter.HumanoidRootPart.Position).unit
                local attractionSpeed = 50
                game:GetService("TweenService"):Create(otherCharacter.HumanoidRootPart, TweenInfo.new(distance / attractionSpeed), {
                    Position = ball.Position
                }):Play()
                otherCharacter:SetPrimaryPartCFrame(ball.CFrame)
            end
        end
    end

    wait(1)
    ball:Destroy()
end

-- Função para criar a bola vermelha com animação do braço
local function createRedBall()
    local ball = Instance.new("Part")
    ball.Size = Vector3.new(5, 5, 5)
    ball.Shape = Enum.PartType.Ball
    ball.Position = character.HumanoidRootPart.Position + character.HumanoidRootPart.CFrame.LookVector * 5
    ball.Anchored = true
    ball.BrickColor = BrickColor.new("Bright red")
    ball.Material = Enum.Material.Neon
    ball.CanCollide = true
    ball.Parent = workspace

    -- Animação de levantar o braço direito
    local rightArm = character:FindFirstChild("RightUpperArm")
    if rightArm then
        local goal = rightArm.CFrame * CFrame.Angles(math.rad(45), 0, 0)  -- Levanta o braço 45 graus
        local tweenInfo = TweenInfo.new(0.5, Enum.EasingStyle.Linear)
        local tween = tweenService:Create(rightArm, tweenInfo, {CFrame = goal})
        tween:Play()
    end

    local currentPosition = ball.Position
    local moveDistance = 10
    local moveCount = 0

    local function moveBall()
        while moveCount < 10 do
            currentPosition = currentPosition + character.HumanoidRootPart.CFrame.LookVector * moveDistance
            ball.Position = currentPosition
            moveCount = moveCount + 1
            wait(0.1)
        end
    end

    moveBall()

    wait(5)

    -- Animação de abaixar o braço direito
    if rightArm then
        local goal = rightArm.CFrame * CFrame.Angles(math.rad(-45), 0, 0)  -- Abaixa o braço 45 graus
        local tweenInfo = TweenInfo.new(0.5, Enum.EasingStyle.Linear)
        local tween = tweenService:Create(rightArm, tweenInfo, {CFrame = goal})
        tween:Play()
    end

    ball:Destroy()
end

-- Função para criar a bola roxa escura
local function createPurpleBall()
    local ball = Instance.new("Part")
    ball.Size = Vector3.new(10, 10, 10)
    ball.Shape = Enum.PartType.Ball
    ball.Position = character.HumanoidRootPart.Position + character.HumanoidRootPart.CFrame.LookVector * 5
    ball.Anchored = true
    ball.BrickColor = BrickColor.new("Bright violet")
    ball.Material = Enum.Material.Neon
    ball.CanCollide = true
    ball.Parent = workspace

    local currentPosition = ball.Position
    local moveDistance = 10
    local moveCount = 0

    local function moveBall()
        while moveCount < 10 do
            currentPosition = currentPosition + character.HumanoidRootPart.CFrame.LookVector * moveDistance
            ball.Position = currentPosition
            moveCount = moveCount + 1
            wait(0.05)
        end
    end

    moveBall()

    wait(5)
    ball:Destroy()
end

-- Detecta quando o jogador pressiona a tecla Z, X ou C
userInputService.InputBegan:Connect(function(input, isProcessed)
    if isProcessed then return end
    if input.KeyCode == Enum.KeyCode.Z then
        sendChatMessage("Lapse: Blue")
        createBlueBall()
    elseif input.KeyCode == Enum.KeyCode.X then
        sendChatMessage("Reversal: Red")
        createRedBall()
    elseif input.KeyCode == Enum.KeyCode.C then
        sendChatMessage("Hollow Purple")
        createPurpleBall()
    end
end)
