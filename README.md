-- Kauan Mods v1.9 (Fix para abrir menu no Delta + FPS Boost aprimorado)
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "KauanMods"
ScreenGui.Parent = PlayerGui
ScreenGui.ResetOnSpawn = false

-- Botão flutuante para abrir o menu (agora com MouseButton1Click para mobile)
local FloatButton = Instance.new("TextButton")
FloatButton.Name = "FloatButton"
FloatButton.Parent = ScreenGui
FloatButton.BackgroundColor3 = Color3.fromRGB(0, 162, 255)
FloatButton.BorderSizePixel = 0
FloatButton.Position = UDim2.new(1, -60, 0, 20)
FloatButton.Size = UDim2.new(0, 50, 0, 50)
FloatButton.Font = Enum.Font.SourceSansBold
FloatButton.Text = "⚡"  -- Emoji de raio
FloatButton.TextColor3 = Color3.fromRGB(255, 255, 0)  -- Amarelo
FloatButton.TextSize = 30
FloatButton.Active = true
FloatButton.Draggable = true  -- Permite arrastar

-- Frame principal (layout menor, altura 160px)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -80)
MainFrame.Size = UDim2.new(0, 300, 0, 160)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = false

-- Título
local Title = Instance.new("TextLabel")
Title.Name = "Title"
Title.Parent = MainFrame
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 0, 0, 0)
Title.Size = UDim2.new(1, 0, 0, 25)
Title.Font = Enum.Font.SourceSansBold
Title.Text = "Kauan Mods"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 16

-- Botão FPS Boost
local FPSButton = Instance.new("TextButton")
FPSButton.Name = "FPSButton"
FPSButton.Parent = MainFrame
FPSButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
FPSButton.BorderSizePixel = 0
FPSButton.Position = UDim2.new(0, 10, 0, 30)
FPSButton.Size = UDim2.new(0, 130, 0, 25)
FPSButton.Font = Enum.Font.SourceSans
FPSButton.Text = "Ativar FPS Boost"
FPSButton.TextColor3 = Color3.fromRGB(255, 255, 255)
FPSButton.TextSize = 12

-- Função FPS Boost aprimorada (remove lighting + desabilita efeitos visuais extras)
local fpsBoostActive = false
local originalQuality = settings().Rendering.QualityLevel
local originalShadows = Lighting.GlobalShadows
local originalFogEnd = Lighting.FogEnd
local originalBrightness = Lighting.Brightness
local originalEffects = {}  -- Salva efeitos do Lighting
FPSButton.MouseButton1Click:Connect(function()  -- Mudei para MouseButton1Click pra compatibilidade mobile
    fpsBoostActive = not fpsBoostActive
    if fpsBoostActive then
        originalQuality = settings().Rendering.QualityLevel
        originalShadows = Lighting.GlobalShadows
        originalFogEnd = Lighting.FogEnd
        originalBrightness = Lighting.Brightness
        -- Salva estados dos efeitos
        for _, effect in pairs(Lighting:GetChildren()) do
            if effect:IsA("PostEffect") then
                originalEffects[effect] = effect.Enabled
                effect.Enabled = false
            end
        end
        settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
        Lighting.GlobalShadows = false
        Lighting.FogEnd = 9e9
        Lighting.Brightness = 0  -- Escurece mais pra boost (ajuste se quiser)
        FPSButton.Text = "Desativar FPS Boost"
    else
        settings().Rendering.QualityLevel = originalQuality
        Lighting.GlobalShadows = originalShadows
        Lighting.FogEnd = originalFogEnd
        Lighting.Brightness = originalBrightness
        -- Restaura efeitos
        for effect, enabled in pairs(originalEffects) do
            if effect.Parent then
                effect.Enabled = enabled
            end
        end
        originalEffects = {}
        FPSButton.Text = "Ativar FPS Boost"
    end
end)

-- Botão Tela Esticada
local StretchButton = Instance.new("TextButton")
StretchButton.Name = "StretchButton"
StretchButton.Parent = MainFrame
StretchButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
StretchButton.BorderSizePixel = 0
StretchButton.Position = UDim2.new(0, 150, 0, 30)
StretchButton.Size = UDim2.new(0, 130, 0, 25)
StretchButton.Font = Enum.Font.SourceSans
StretchButton.Text = "Tela Esticada"
StretchButton.TextColor3 = Color3.fromRGB(255, 255, 255)
StretchButton.TextSize = 12

local stretchActive = false
local originalFOV = workspace.CurrentCamera.FieldOfView
local fovConnection
StretchButton.MouseButton1Click:Connect(function()  -- Mudei pra MouseButton1Click
    stretchActive = not stretchActive
    if stretchActive then
        originalFOV = workspace.CurrentCamera.FieldOfView
        if fovConnection then
            fovConnection:Disconnect()
        end
        workspace.CurrentCamera.FieldOfView = 110
        fovConnection = RunService.Heartbeat:Connect(function()
            if stretchActive then
                workspace.CurrentCamera.FieldOfView = 110
            end
        end)
        StretchButton.Text = "Desativar Esticada"
    else
        if fovConnection then
            fovConnection:Disconnect()
            fovConnection = nil
        end
        workspace.CurrentCamera.FieldOfView = originalFOV
        StretchButton.Text = "Tela Esticada"
    end
end)

-- Botão Steal/TP
local TPButton = Instance.new("TextButton")
TPButton.Name = "TPButton"
TPButton.Parent = MainFrame
TPButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
TPButton.BorderSizePixel = 0
TPButton.Position = UDim2.new(0, 10, 0, 60)
TPButton.Size = UDim2.new(0, 270, 0, 25)
TPButton.Font = Enum.Font.SourceSans
TPButton.Text = "Steal/TP para Player Mais Próximo"
TPButton.TextColor3 = Color3.fromRGB(255, 255, 255)
TPButton.TextSize = 12

TPButton.MouseButton1Click:Connect(function()  -- Mudei pra MouseButton1Click
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then return end
    local closestPlayer = nil
    local shortestDistance = math.huge
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local distance = (LocalPlayer.Character.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
            if distance < shortestDistance then
                shortestDistance = distance
                closestPlayer = player
            end
        end
    end
    if closestPlayer and closestPlayer.Character and closestPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = closestPlayer.Character.HumanoidRootPart.CFrame
    end
end)

-- Botão Fechar
local CloseButton = Instance.new("TextButton")
CloseButton.Name = "CloseButton"
CloseButton.Parent = MainFrame
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
CloseButton.BorderSizePixel = 0
CloseButton.Position = UDim2.new(1, -30, 0, 0)
CloseButton.Size = UDim2.new(0, 30, 0, 25)
CloseButton.Font = Enum.Font.SourceSansBold
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.TextSize = 14

CloseButton.MouseButton1Click:Connect(function()  -- Mudei pra MouseButton1Click
    MainFrame.Visible = false
end)

-- Assinatura
local Signature = Instance.new("TextLabel")
Signature.Name = "Signature"
Signature.Parent = MainFrame
Signature.BackgroundTransparency = 1
Signature.Position = UDim2.new(0, 0, 0, 130)
Signature.Size = UDim2.new(1, 0, 0, 25)
Signature.Font = Enum.Font.SourceSans
Signature.Text = "@kauanmods"
Signature.TextColor3 = Color3.fromRGB(150, 150, 150)
Signature.TextSize = 10
Signature.TextXAlignment = Enum.TextXAlignment.Center

-- Toggle do menu (usando MouseButton1Click pro Delta/mobile)
local guiVisible = false
FloatButton.MouseButton1Click:Connect(function()
    guiVisible = not guiVisible
    MainFrame.Visible = guiVisible
end)

-- Backup para PC: Tecla "K"
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.K then
        guiVisible = not guiVisible
        MainFrame.Visible = guiVisible
    end
end)

print("Kauan Mods v1.9 carregado! Fix: Menu abre com toque no ⚡ (MouseButton1Click). FPS Boost agora escurece mais + desabilita post-effects pra boost real!")
