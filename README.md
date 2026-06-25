--[[
    SCRIPT ROBLOX - TTP HUB
    Compatível: Delta | Fluxus
    Versão: 3.4.3
    ✅ GUI ANIMADA E ARRASTÁVEL
    ✅ Teleporte atrás do alvo
    ✅ ESP SEM DUPLICATAS | SEM NOMES | FUNCIONA COM GUI FECHADA
    ✅ Aimbot corrigido e funcional
]]

-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local StarterGui = game:GetService("StarterGui")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local Workspace = game:GetService("Workspace")
local LocalPlayer = Players.LocalPlayer

-- 🔑 CONFIGURAÇÃO
local DONO_NOME = "xxtire10" -- Coloque seu nome exato do Roblox

-- Variáveis globais
local Character, Humanoid, RootPart
local function AtualizarPersonagem()
    Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    Humanoid = Character:WaitForChild("Humanoid", 10)
    RootPart = Character:WaitForChild("HumanoidRootPart", 10)
end
AtualizarPersonagem()
LocalPlayer.CharacterAdded:Connect(AtualizarPersonagem)

setclipboard("https://discord.gg/MEj6gunVg")
getgenv().UsandoMeuScript = true

if getgenv().TTPHub then getgenv().TTPHub:Destroy() end
getgenv().TTPHub = Instance.new("ScreenGui")
getgenv().TTPHub.Name = "TTP_HUB"
getgenv().TTPHub.ResetOnSpawn = false
getgenv().TTPHub.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
getgenv().TTPHub.Parent = game:GetService("CoreGui")

local GuiVisivel = true
local VelAtual = 16
local ESPAtivo = false
local AimbotAtivo = false
local AlcanceAimbot = 200
local Hue = 0
local FPS = 0
local ArrastandoJanela, InicioPosJanela, InicioToqueJanela = false
local ArrastandoBotao, InicioPosBotao, InicioToqueBotao = false

-- Função auxiliar
local function CriarElemento(tipo, propriedades, pai)
    local obj = Instance.new(tipo)
    for k, v in pairs(propriedades) do obj[k] = v end
    obj.Parent = pai
    return obj
end

-- 🎨 JANELA PRINCIPAL
local Janela = CriarElemento("Frame", {
    Size = UDim2.new(0, 280, 0, 440),
    Position = UDim2.new(0.02, 0, 0.1, 0),
    BackgroundColor3 = Color3.fromRGB(20, 20, 30),
    BorderSizePixel = 0,
    ClipsDescendants = true,
    Visible = true,
    Transparency = 0
}, getgenv().TTPHub)
CriarElemento("UICorner", {CornerRadius = UDim.new(0, 10)}, Janela)

-- Animação entrada
Janela.Position = UDim2.new(0.02, 0, 0.1, -500)
TweenService:Create(Janela, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
    Position = UDim2.new(0.02, 0, 0.1, 0), Transparency = 0
}):Play()

-- Barra superior
local Barra = CriarElemento("Frame", {
    Size = UDim2.new(1, 0, 0, 35),
    BackgroundColor3 = Color3.fromRGB(35, 35, 60)
}, Janela)
CriarElemento("UICorner", {CornerRadius = UDim.new(0, 10)}, Barra)
CriarElemento("TextLabel", {
    Size = UDim2.new(1, -40, 1, 0), Position = UDim2.new(0,10,0,0),
    BackgroundTransparency = 1, Text = "✨ TTP HUB", TextColor3 = Color3.fromRGB(220,220,255),
    TextSize = 14, Font = Enum.Font.GothamBold, TextXAlignment = Enum.TextXAlignment.Left
}, Barra)

local BotaoFechar = CriarElemento("TextButton", {
    Size = UDim2.new(0,30,0,25), Position = UDim2.new(1,-35,0,5),
    BackgroundColor3 = Color3.fromRGB(90,40,60), Text = "✕", TextColor3 = Color3.new(1,1,1),
    TextSize = 13, Font = Enum.Font.GothamBold
}, Barra)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,6)}, BotaoFechar)

local BotaoAbrir = CriarElemento("TextButton", {
    Size = UDim2.new(0,60,0,35), Position = UDim2.new(0.02,0,0.1,0),
    BackgroundColor3 = Color3.fromRGB(40,60,100), Text = "ABRIR", TextColor3 = Color3.new(1,1,1),
    TextSize = 12, Font = Enum.Font.GothamBold, Visible = false
}, getgenv().TTPHub)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, BotaoAbrir)

-- Contador FPS
local TextoFPS = CriarElemento("TextLabel", {
    Size = UDim2.new(0,85,0,30), Position = UDim2.new(1,-90,0,10),
    BackgroundColor3 = Color3.fromRGB(30,30,50), BackgroundTransparency = 0.2,
    Text = "FPS: 0", TextColor3 = Color3.new(1,1,1), TextSize = 13, Font = Enum.Font.GothamBold
}, getgenv().TTPHub)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, TextoFPS)

task.spawn(function()
    local frameCount, lastTime = 0, os.clock()
    while task.wait() do
        frameCount += 1
        local now = os.clock()
        if now - lastTime >= 1 then
            FPS = math.floor(frameCount / (now - lastTime))
            TextoFPS.Text = "FPS: "..FPS
            TweenService:Create(TextoFPS, TweenInfo.new(0.2), {
                TextColor3 = FPS >= 50 and Color3.fromRGB(80,255,120) or FPS >= 30 and Color3.fromRGB(255,230,80) or Color3.fromRGB(255,90,90)
            }):Play()
            frameCount, lastTime = 0, now
        end
    end
end)

-- Alternar GUI
local function AlternarGUI()
    GuiVisivel = not GuiVisivel
    if GuiVisivel then
        TweenService:Create(Janela, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            Position = UDim2.new(0.02,0,0.1,0), Visible = true, Transparency = 0
        }):Play()
        task.wait(0.2)
        BotaoAbrir.Visible = false
    else
        TweenService:Create(Janela, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {
            Position = UDim2.new(0.02,0,0.1,-500), Transparency = 0.3
        }):Play()
        task.wait(0.4)
        Janela.Visible = false
        BotaoAbrir.Visible = true
    end
end
BotaoFechar.MouseButton1Click:Connect(AlternarGUI)
BotaoAbrir.MouseButton1Click:Connect(AlternarGUI)

-- Arrastar janela
Barra.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseButton1 then
        ArrastandoJanela = true
        InicioPosJanela = Janela.Position
        InicioToqueJanela = Vector2.new(i.Position.X, i.Position.Y)
    end
end)
BotaoAbrir.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseButton1 then
        ArrastandoBotao = true
        InicioPosBotao = BotaoAbrir.Position
        InicioToqueBotao = Vector2.new(i.Position.X, i.Position.Y)
    end
end)
UserInputService.InputChanged:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseMovement then
        if ArrastandoJanela then
            local mov = Vector2.new(i.Position.X, i.Position.Y) - InicioToqueJanela
            Janela.Position = UDim2.new(InicioPosJanela.X.Scale, InicioPosJanela.X.Offset + mov.X, InicioPosJanela.Y.Scale, InicioPosJanela.Y.Offset + mov.Y)
        end
        if ArrastandoBotao then
            local mov = Vector2.new(i.Position.X, i.Position.Y) - InicioToqueBotao
            BotaoAbrir.Position = UDim2.new(InicioPosBotao.X.Scale, InicioPosBotao.X.Offset + mov.X, InicioPosBotao.Y.Scale, InicioPosBotao.Y.Offset + mov.Y)
        end
    end
end)
UserInputService.InputEnded:Connect(function() ArrastandoJanela = false; ArrastandoBotao = false end)

-- Abas
local PainelAbas = CriarElemento("Frame", {Size = UDim2.new(1,-10,0,35), Position = UDim2.new(0,5,0,45), BackgroundTransparency = 1}, Janela)
local BotaoAba1 = CriarElemento("TextButton", {Size = UDim2.new(0.23,0,1,0), BackgroundColor3 = Color3.fromRGB(45,45,75), Text = "⚙️ GERAL", TextColor3 = Color3.new(1,1,1), TextSize = 10, Font = Enum.Font.GothamBold}, PainelAbas)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, BotaoAba1)
local BotaoAba2 = CriarElemento("TextButton", {Size = UDim2.new(0.23,0,1,0), Position = UDim2.new(0.26,0,0,0), BackgroundColor3 = Color3.fromRGB(35,35,60), Text = "👁️ ESP", TextColor3 = Color3.fromRGB(200,200,220), TextSize = 10, Font = Enum.Font.GothamBold}, PainelAbas)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, BotaoAba2)
local BotaoAba3 = CriarElemento("TextButton", {Size = UDim2.new(0.23,0,1,0), Position = UDim2.new(0.52,0,0,0), BackgroundColor3 = Color3.fromRGB(35,35,60), Text = "🎯 AIM", TextColor3 = Color3.fromRGB(200,200,220), TextSize = 10, Font = Enum.Font.GothamBold}, PainelAbas)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, BotaoAba3)
local BotaoAba4 = CriarElemento("TextButton", {Size = UDim2.new(0.23,0,1,0), Position = UDim2.new(0.78,0,0,0), BackgroundColor3 = Color3.fromRGB(35,35,60), Text = "ℹ️ INFO", TextColor3 = Color3.fromRGB(200,200,220), TextSize = 10, Font = Enum.Font.GothamBold}, PainelAbas)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, BotaoAba4)

-- Conteúdo das abas
local ConteudoAba1 = CriarElemento("ScrollingFrame", {Size = UDim2.new(1,-10,1,-85), Position = UDim2.new(0,5,0,80), BackgroundTransparency = 1, ScrollBarThickness = 4, ScrollBarImageColor3 = Color3.fromRGB(80,80,150), CanvasSize = UDim2.new(0,0,0,320), Visible = true}, Janela)
local ConteudoAba2 = CriarElemento("Frame", {Size = UDim2.new(1,-10,1,-85), Position = UDim2.new(0,5,0,80), BackgroundColor3 = Color3.fromRGB(25,25,45), BackgroundTransparency = 0.3, Visible = false}, Janela)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, ConteudoAba2)
local ConteudoAba3 = CriarElemento("Frame", {Size = UDim2.new(1,-10,1,-85), Position = UDim2.new(0,5,0,80), BackgroundColor3 = Color3.fromRGB(25,25,45), BackgroundTransparency = 0.3, Visible = false}, Janela)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, ConteudoAba3)
local ConteudoAba4 = CriarElemento("Frame", {Size = UDim2.new(1,-10,1,-85), Position = UDim2.new(0,5,0,80), BackgroundColor3 = Color3.fromRGB(25,25,45), BackgroundTransparency = 0.3, Visible = false}, Janela)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, ConteudoAba4)

local function MostrarAba(n)
    local botoes = {BotaoAba1, BotaoAba2, BotaoAba3, BotaoAba4}
    for i, btn in ipairs(botoes) do
        TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = i == n and Color3.fromRGB(55,55,95) or Color3.fromRGB(40,40,70)}):Play()
        TweenService:Create(btn, TweenInfo.new(0.2), {TextColor3 = i == n and Color3.new(1,1,1) or Color3.fromRGB(180,180,200)}):Play()
    end
    ConteudoAba1.Visible = n == 1; ConteudoAba2.Visible = n == 2; ConteudoAba3.Visible = n == 3; ConteudoAba4.Visible = n == 4
end
BotaoAba1.MouseButton1Click:Connect(function() MostrarAba(1) end)
BotaoAba2.MouseButton1Click:Connect(function() MostrarAba(2) end)
BotaoAba3.MouseButton1Click:Connect(function() MostrarAba(3) end)
BotaoAba4.MouseButton1Click:Connect(function() MostrarAba(4) end)

-- ⚙️ GERAL
local LayoutAba1 = CriarElemento("UIListLayout", {Padding = UDim.new(0,12), HorizontalAlignment = Enum.HorizontalAlignment.Center}, ConteudoAba1)
local GrupoVel = CriarElemento("Frame", {Size = UDim2.new(0.95,0,0,90), BackgroundColor3 = Color3.fromRGB(35,35,65), BackgroundTransparency = 0.2}, ConteudoAba1)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, GrupoVel)
CriarElemento("TextLabel", {Size = UDim2.new(0.95,0,0,25), Position = UDim2.new(0.025,0,0,5), BackgroundTransparency = 1, Text = "🏃 VELOCIDADE", TextColor3 = Color3.fromRGB(220,220,255), TextSize = 13, Font = Enum.Font.GothamBold}, GrupoVel)
local CaixaVel = CriarElemento("TextBox", {Size = UDim2.new(0.7,0,0,35), Position = UDim2.new(0.05,0,0,35), BackgroundColor3 = Color3.fromRGB(50,50,90), Text = "16", PlaceholderText = "Ex: 16 até 200", TextColor3 = Color3.new(1,1,1), TextSize = 13, Font = Enum.Font.Gotham, ClearTextOnFocus = false}, GrupoVel)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, CaixaVel)
local BotaoVel = CriarElemento("TextButton", {Size = UDim2.new(0.2,0,0,35), Position = UDim2.new(0.77,0,0,35), BackgroundColor3 = Color3.fromRGB(60,120,90), Text = "APLICAR", TextColor3 = Color3.new(1,1,1), TextSize = 12, Font = Enum.Font.GothamBold}, GrupoVel)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, BotaoVel)

local function AplicarVel()
    local v = tonumber(CaixaVel.Text)
    if v and v > 0 and v <= 500 then
        VelAtual = math.floor(v)
        TweenService:Create(BotaoVel, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(80,180,120)}):Play()
        task.wait(0.2)
        TweenService:Create(BotaoVel, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(60,120,90)}):Play()
        StarterGui:SetCore("SendNotification", {Title = "✅ Velocidade", Text = "Definida: "..VelAtual})
    else
        CaixaVel.Text = tostring(VelAtual)
        StarterGui:SetCore("SendNotification", {Title = "❌ Erro", Text = "Digite um número válido"})
    end
end
BotaoVel.MouseButton1Click:Connect(AplicarVel)

-- Lista de jogadores / Teleporte
local GrupoJog = CriarElemento("Frame", {Size = UDim2.new(0.95,0,0,170), BackgroundColor3 = Color3.fromRGB(35,35,65), BackgroundTransparency = 0.2}, ConteudoAba1)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, GrupoJog)
CriarElemento("TextLabel", {Size = UDim2.new(0.95,0,0,25), Position = UDim2.new(0.025,0,0,5), BackgroundTransparency = 1, Text = "👥 JOGADORES | TELEPORTE ATRÁS", TextColor3 = Color3.fromRGB(220,220,255), TextSize = 13, Font = Enum.Font.GothamBold}, GrupoJog)
local ListaJog = CriarElemento("ScrollingFrame", {Size = UDim2.new(0.95,0,0,130), Position = UDim2.new(0.025,0,0,35), BackgroundColor3 = Color3.fromRGB(25,25,50), BackgroundTransparency = 0.3, ScrollBarThickness = 4, ScrollBarImageColor3 = Color3.fromRGB(80,80,150), CanvasSize = UDim2.new(0,0,0,200)}, GrupoJog)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,6)}, ListaJog)
local LayoutLista = CriarElemento("UIListLayout", {Padding = UDim.new(0,6), HorizontalAlignment = Enum.HorizontalAlignment.Center}, ListaJog)

local function Teleportar(alvo)
    if not alvo or alvo == LocalPlayer then return end
    local c = alvo.Character
    if c and c:FindFirstChild("HumanoidRootPart") and Humanoid and Humanoid.Health > 0 then
        local rootAlvo = c.HumanoidRootPart
        local direcao = rootAlvo.CFrame.LookVector
        local posAtras = rootAlvo.Position - direcao * 1.5 + Vector3.new(0,1,0)
        Character:SetPrimaryPartCFrame(CFrame.new(posAtras, rootAlvo.Position))
        StarterGui:SetCore("SendNotification", {Title = "⚡ Teleporte", Text = "Você foi para trás de "..alvo.Name})
    end
end

local function AtualizarLista()
    for _,f in ipairs(ListaJog:GetChildren()) do if f:IsA("Frame") then f:Destroy() end end
    for _,j in ipairs(Players:GetPlayers()) do
        if j ~= LocalPlayer then
            local Linha = CriarElemento("Frame", {Size = UDim2.new(0.95,0,0,32), BackgroundColor3 = Color3.fromRGB(45,45,80), BackgroundTransparency = 0.2}, ListaJog)
            CriarElemento("UICorner", {CornerRadius = UDim.new(0,6)}, Linha)
            local Foto = CriarElemento("ImageLabel", {Size = UDim2.new(0,26,0,26), Position = UDim2.new(0,3,0,3), BackgroundColor3 = Color3.fromRGB(60,60,100), Image = Players:GetUserThumbnailAsync(j.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size48x48)}, Linha)
            CriarElemento("UICorner", {CornerRadius = UDim.new(1,0)}, Foto)
            local Nome = CriarElemento("TextLabel", {Size = UDim2.new(0.65,0,1,0), Position = UDim2.new(0,35,0,0), BackgroundTransparency = 1, Text = j.Name, TextColor3 = Color3.new(1,1,1), TextSize = 11, Font = Enum.Font.GothamSemibold, TextXAlignment = Enum.TextXAlignment.Left}, Linha)
            local BotaoTp = CriarElemento("TextButton", {Size = UDim2.new(0.25,0,0.8,0), Position = UDim2.new(0.72,0,0.1,0), BackgroundColor3 = Color3.fromRGB(70,100,160), Text = "IR", TextColor3 = Color3.new(1,1,1), TextSize = 11, Font = Enum.Font.GothamBold}, Linha)
            CriarElemento("UICorner", {CornerRadius = UDim.new(0,6)}, BotaoTp)
            BotaoTp.MouseButton1Click:Connect(function() Teleportar(j) end)
        end
    end
    ListaJog.CanvasSize = UDim2.new(0,0,0,LayoutLista.AbsoluteContentSize.Y)
end
Players.PlayerAdded:Connect(AtualizarLista)
Players.PlayerRemoving:Connect(AtualizarLista)
AtualizarLista()

-- 👁️ ESP
local BotaoESP = CriarElemento("TextButton", {Size = UDim2.new(0.8,0,0,40), Position = UDim2.new(0.1,0,0.1,0), BackgroundColor3 = Color3.fromRGB(60,90,140), Text = "ATIVAR ESP RGB", TextColor3 = Color3.new(1,1,1), TextSize = 13, Font = Enum.Font.GothamBold}, ConteudoAba2)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, BotaoESP)
local function AlternarESP()
    ESPAtivo = not ESPAtivo
    TweenService:Create(BotaoESP, TweenInfo.new(0.2), {BackgroundColor3 = ESPAtivo and Color3.fromRGB(80,140,220) or Color3.fromRGB(60,90,140)}):Play()
    BotaoESP.Text = ESPAtivo and "✅ ESP ATIVADO" or "❌ ESP DESATIVADO"
end
BotaoESP.MouseButton1Click:Connect(AlternarESP)

-- 🎯 AIM
local BotaoAim = CriarElemento("TextButton", {Size = UDim2.new(0.8,0,0,40), Position = UDim2.new(0.1,0,0.1,0), BackgroundColor3 = Color3.fromRGB(140,60,90), Text = "ATIVAR AIMBOT", TextColor3 = Color3.new(1,1,1), TextSize = 13, Font = Enum.Font.GothamBold}, ConteudoAba3)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, BotaoAim)
CriarElemento("TextLabel", {Size = UDim2.new(0.8,0,0,25), Position = UDim2.new(0.1,0,0.28,0), BackgroundTransparency = 1, Text = "📏 ALCANCE MÁXIMO (m)", TextColor3 = Color3.fromRGB(220,220,255), TextSize = 12, Font = Enum.Font.GothamSemibold}, ConteudoAba3)
local CaixaAlcance = CriarElemento("TextBox", {Size = UDim2.new(0.55,0,0,35), Position = UDim2.new(0.1,0,0.4,0), BackgroundColor3 = Color3.fromRGB(50,50,90), Text = "200", PlaceholderText = "Ex: 50 a 500", TextColor3 = Color3.new(1,1,1), TextSize = 13, Font = Enum.Font.Gotham, ClearTextOnFocus = false}, ConteudoAba3)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, CaixaAlcance)
local BotaoAlcance = CriarElemento("TextButton", {Size = UDim2.new(0.25,0,0,35), Position = UDim2.new(0.7,0,0.4,0), BackgroundColor3 = Color3.fromRGB(60,120,90), Text = "DEFINIR", TextColor3 = Color3.new(1,1,1), TextSize = 12, Font = Enum.Font.GothamBold}, ConteudoAba3)
CriarElemento("UICorner", {CornerRadius = UDim.new(0,8)}, BotaoAlcance)

local function AlternarAimbot()
    AimbotAtivo = not AimbotAtivo
    TweenService:Create(BotaoAim, TweenInfo.new(0.2), {BackgroundColor3 = AimbotAtivo and Color3.fromRGB(220,80,120) or Color3.fromRGB(140,60,90)}):Play()
    BotaoAim.Text = AimbotAtivo and "✅ AIMBOT ATIVO" or "❌ AIMBOT DESATIVADO"
end
BotaoAim.MouseButton1Click:Connect(AlternarAimbot)

local function DefinirAlcance()
    local val = tonumber(CaixaAlcance.Text)
    if val and val >= 20 and val <= 800 then
        AlcanceAimbot = val
        StarterGui:SetCore("SendNotification", {Title = "📏 Alcance", Text = "Definido: "..AlcanceAimbot.."m"})
    else
        CaixaAlcance.Text = tostring(AlcanceAimbot)
        StarterGui:SetCore("SendNotification", {Title = "❌ Erro", Text = "Valor entre 20 e 800"})
    end
end
BotaoAlcance.MouseButton1Click:Connect(DefinirAlcance)

-- Lógica Aimbot
task.spawn(function()
    while task.wait(0.015) do
        if not AimbotAtivo or not Character or not Humanoid or not RootPart or Humanoid.Health <= 0 then task.wait(0.1); continue end
        local melhorAlvo, menorDist = nil, math.huge
        local posCamera = Camera.CFrame.Position
        for _, jogador in ipairs(Players:GetPlayers()) do
            if jogador == LocalPlayer then continue end
            local char = jogador.Character
            if not char or not char:FindFirstChild("Head") or not char:FindFirstChild("HumanoidRootPart") or char.Humanoid.Health <= 0 then continue end
            local cabeca = char.Head
            local dist = (RootPart.Position - cabeca.Position).Magnitude
            if dist <= AlcanceAimbot then
                local direcao = (cabeca.Position - posCamera).Unit
                local raio = Ray.new(posCamera, direcao * dist)
                local parteAtingida = Workspace:FindPartOnRayWithIgnoreList(raio, {Character, Camera, workspace.Terrain})
                if parteAtingida and parteAtingida:IsDescendantOf(char) and dist < menorDist then
                    menorDist = dist; melhorAlvo = cabeca
                end
            end
        end
        if melhorAlvo then Camera.CFrame = Camera.CFrame:Lerp(CFrame.new(posCamera, melhorAlvo.Position), 0.25) end
    end
end)

-- 📌 ESP CORRIGIDO: SEM NOMES | SEM DUPLICATAS
task.spawn(function()
    local desenhos = {}
    while task.wait(0.03) do
        -- Limpa tudo antes de desenhar novamente
        for _, d in ipairs(desenhos) do if d and d.Remove then d:Remove() end end
        table.clear(desenhos)

        -- Verificações básicas
        if not Character or not RootPart or not Humanoid or Humanoid.Health <= 0 then task.wait(0.1); continue end
        if not ESPAtivo then task.wait(0.1); continue end

        Hue = (Hue + 0.002) % 1
        local CorRGB = Color3.fromHSV(Hue, 1, 1)

        for _, j in ipairs(Players:GetPlayers()) do
            if j == LocalPlayer then continue end
            local c = j.Character
            if not c or not c:FindFirstChild("HumanoidRootPart") or not c:FindFirstChild("Head") or c.Humanoid.Health <= 0 then continue end

            local rootAlvo = c.HumanoidRootPart
            local cabecaAlvo = c.Head
            local posCima, visCima = Camera:WorldToViewportPoint(cabecaAlvo.Position + Vector3.new(0, 2, 0.2))
            local posBase, visBase = Camera:WorldToViewportPoint(rootAlvo.Position - Vector3.new(0, 1.5, 0))

            if not visCima or posCima.Z < 1 or not visBase or posBase.Z < 1 then continue end

            local altura = math.abs(posCima.Y - posBase.Y)
            local largura = altura * 0.45
            local esq = posCima.X - largura/2
            local dir = posCima.X + largura/2
            local bai = posBase.Y
            local cim = posCima.Y

            -- Apenas o quadrado e linha de mira, sem nomes
            local l1 = Drawing.new("Line") l1.From = Vector2.new(esq,cim) l1.To = Vector2.new(dir,cim) l1.Color = CorRGB l1.Thickness = 1.5 l1.Visible = true table.insert(desenhos, l1)
            local l2 = Drawing.new("Line") l2.From = Vector2.new(esq,bai) l2.To = Vector2.new(dir,bai) l2.Color = CorRGB l2.Thickness = 1.5 l2.Visible = true table.insert(desenhos, l2)
            local l3 = Drawing.new("Line") l3.From = Vector2.new(esq,cim) l3.To = Vector2.new(esq,bai) l3.Color = CorRGB l3.Thickness = 1.5 l3.Visible = true table.insert(desenhos, l3)
            local l4 = Drawing.new("Line") l4.From = Vector2.new(dir,cim) l4.To = Vector2.new(dir,bai) l4.Color = CorRGB l4.Thickness = 1.5 l4.Visible = true table.insert(desenhos, l4)

            local centroTela = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y)
            local mira = Drawing.new("Line") mira.From = centroTela mira.To = Vector2.new((esq+dir)/2, bai) mira.Color = CorRGB mira.Thickness = 1 mira.Visible = true table.insert(desenhos, mira)
        end
    end
end)

-- ℹ️ INFO
CriarElemento("TextLabel", {Size = UDim2.new(0.9,0,0,30), Position = UDim2.new(0.05,0,0.1,0), BackgroundTransparency = 1, Text = "✨ TTP HUB", TextColor3 = Color3.fromRGB(220,220,255), TextSize = 15, Font = Enum.Font.GothamBold}, ConteudoAba4)
CriarElemento("TextLabel", {Size = UDim2.new(0.9,0,0,25), Position = UDim2.new(0.05,0,0.22,0), BackgroundTransparency = 1, Text = "👑 Dono: "..DONO_NOME, TextColor3 = Color3.fromRGB(255,215,0), TextSize = 13, Font = Enum.Font.GothamSemibold}, ConteudoAba4)
CriarElemento("TextLabel", {Size = UDim2.new(0.9,0,0,25), Position = UDim2.new(0.05,0,0.35,0), BackgroundTransparency = 1, Text = "✅ Compatível: Delta / Fluxus", TextColor3 = Color3.fromRGB(180,200,255), TextSize = 12, Font = Enum.Font.Gotham}, ConteudoAba4)
CriarElemento("TextLabel", {Size = UDim2.new(0.9,0,0,25), Position = UDim2.new(0.05,0,0.48,0), BackgroundTransparency = 1, Text = "📌 Versão: 3.4.3", TextColor3 = Color3.fromRGB(180,200,255), TextSize = 12, Font = Enum.Font.Gotham}, ConteudoAba4)

-- Manter velocidade após renascer
RunService.RenderStepped:Connect(function()
    if Humanoid and Humanoid.Health > 0 then Humanoid.WalkSpeed = VelAtual end
end)

-- Mensagem final
StarterGui:SetCore("SendNotification", {
    Title = "✅ TTP HUB v3.4.3 CARREGADO",
    Text = "Sem duplicatas e sem nomes!",
    Duration = 3
})
