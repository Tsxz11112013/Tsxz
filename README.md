-- Serviços do Roblox
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local camera = workspace.CurrentCamera
local mouse = player:GetMouse()

-- Chave correta para liberação
local KEY_CORRETA = "Wadm_"
local keyAprovada = false

-- Nova Paleta de Cores Baseada na Imagem (Dark Rose / Neon Pink-Purple)
local COR_FUNDO = Color3.fromRGB(18, 17, 20)          -- Fundo ultra escuro
local COR_CONTEUDO = Color3.fromRGB(24, 23, 27)       -- Fundo dos cards internos
local COR_ROSA_MAIN = Color3.fromRGB(219, 95, 150)    -- Rosa destaque (Luv-U style)
local COR_ROSA_GLOW = Color3.fromRGB(150, 50, 90)     -- Brilho neon suave
local COR_BORDINHA = Color3.fromRGB(42, 38, 45)       -- Bordas discretas
local COR_TEXTO_MAIN = Color3.fromRGB(240, 240, 240)
local COR_TEXTO_SUB = Color3.fromRGB(140, 135, 145)

local CORES_FILTRO = {
	ALL = {Nome = "Todos", Cor = COR_ROSA_MAIN},
	ORANGE = {Nome = "Laranja", Cor = Color3.fromRGB(255, 140, 0)},
	BLUE = {Nome = "Azul", Cor = Color3.fromRGB(0, 120, 255)},
	RED = {Nome = "Vermelho", Cor = Color3.fromRGB(255, 50, 50)}
}
local filtroAtual = "ALL" 

-- ScreenGui Principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "Mrc_++"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui

--- ==========================================
--- 🔑 SISTEMA DE KEY (UI & LÓGICA)
--- ==========================================
local keyPanel = Instance.new("Frame")
keyPanel.Name = "KeyPanel"
keyPanel.Size = UDim2.new(0, 320, 0, 200)
keyPanel.Position = UDim2.new(0.5, -160, 0.5, -100)
keyPanel.BackgroundColor3 = COR_FUNDO
keyPanel.BorderSizePixel = 0
keyPanel.Parent = screenGui

local keyCorner = Instance.new("UICorner")
keyCorner.CornerRadius = UDim.new(0, 8)
keyCorner.Parent = keyPanel

local keyStroke = Instance.new("UIStroke")
keyStroke.Color = COR_ROSA_MAIN
keyStroke.Thickness = 1.5
keyStroke.Parent = keyPanel

local keyTitle = Instance.new("TextLabel")
keyTitle.Size = UDim2.new(1, 0, 0, 45)
keyTitle.BackgroundTransparency = 1
keyTitle.TextColor3 = COR_TEXTO_MAIN
keyTitle.Font = Enum.Font.GothamBold
keyTitle.TextSize = 15
keyTitle.Text = "🔑 Sistema de Verificação"
keyTitle.Parent = keyPanel

local keyTextBox = Instance.new("TextBox")
keyTextBox.Size = UDim2.new(1, -40, 0, 40)
keyTextBox.Position = UDim2.new(0, 20, 0, 60)
keyTextBox.BackgroundColor3 = COR_CONTEUDO
keyTextBox.TextColor3 = COR_TEXTO_MAIN
keyTextBox.PlaceholderColor3 = COR_TEXTO_SUB
keyTextBox.PlaceholderText = "Insira a Key aqui..."
keyTextBox.Font = Enum.Font.GothamMedium
keyTextBox.TextSize = 13
keyTextBox.Text = ""
keyTextBox.BorderSizePixel = 0
keyTextBox.Parent = keyPanel

local ktbCorner = Instance.new("UICorner")
ktbCorner.CornerRadius = UDim.new(0, 6)
ktbCorner.Parent = keyTextBox

local keyVerifyBtn = Instance.new("TextButton")
keyVerifyBtn.Size = UDim2.new(1, -40, 0, 40)
keyVerifyBtn.Position = UDim2.new(0, 20, 0, 120)
keyVerifyBtn.BackgroundColor3 = COR_ROSA_MAIN
keyVerifyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
keyVerifyBtn.Font = Enum.Font.GothamBold
keyVerifyBtn.TextSize = 13
keyVerifyBtn.Text = "Verificar"
keyVerifyBtn.BorderSizePixel = 0
keyVerifyBtn.Parent = keyPanel

local kvbCorner = Instance.new("UICorner")
kvbCorner.CornerRadius = UDim.new(0, 6)
kvbCorner.Parent = keyVerifyBtn

local erroLabel = Instance.new("TextLabel")
erroLabel.Size = UDim2.new(1, 0, 0, 20)
erroLabel.Position = UDim2.new(0, 0, 0, 170)
erroLabel.BackgroundTransparency = 1
erroLabel.TextColor3 = Color3.fromRGB(220, 80, 80)
erroLabel.Font = Enum.Font.GothamSemibold
erroLabel.TextSize = 11
erroLabel.Text = ""
erroLabel.Parent = keyPanel

--- ==========================================
--- 2. PAINEL PRINCIPAL ESTILO LUV-U (Mrc_++)
--- ==========================================
local openButton = Instance.new("TextButton")
openButton.Name = "BotaoAbrir"
openButton.Size = UDim2.new(0, 140, 0, 45)
openButton.Position = UDim2.new(0, 20, 0.85, 0)
openButton.BackgroundColor3 = COR_FUNDO
openButton.TextColor3 = COR_TEXTO_MAIN
openButton.Font = Enum.Font.GothamBold
openButton.TextSize = 13
openButton.Text = "🔓 Mrc_++"
openButton.Visible = false
openButton.BorderSizePixel = 0
openButton.Parent = screenGui

local obCorner = Instance.new("UICorner")
obCorner.CornerRadius = UDim.new(0, 8)
obCorner.Parent = openButton

local openStroke = Instance.new("UIStroke")
openStroke.Color = COR_ROSA_MAIN
openStroke.Thickness = 1
openStroke.Parent = openButton

local mainPanel = Instance.new("Frame")
mainPanel.Name = "MainPanel"
mainPanel.Size = UDim2.new(0, 650, 0, 360) 
mainPanel.Position = UDim2.new(0.5, -325, 0.5, -180)
mainPanel.BackgroundColor3 = COR_FUNDO
mainPanel.BorderSizePixel = 0
mainPanel.ClipsDescendants = true
mainPanel.Visible = false
mainPanel.Parent = screenGui

local mpCorner = Instance.new("UICorner")
mpCorner.CornerRadius = UDim.new(0, 10)
mpCorner.Parent = mainPanel

local panelStroke = Instance.new("UIStroke")
panelStroke.Color = COR_ROSA_GLOW
panelStroke.Thickness = 1.5
panelStroke.Parent = mainPanel

-- 1. HEADER (BARRA SUPERIOR)
local header = Instance.new("Frame")
header.Name = "Header"
header.Size = UDim2.new(1, 0, 0, 55)
header.BackgroundTransparency = 1
header.Parent = mainPanel

local logoIcon = Instance.new("TextLabel")
logoIcon.Size = UDim2.new(0, 40, 0, 40)
logoIcon.Position = UDim2.new(0, 15, 0, 8)
logoIcon.BackgroundTransparency = 1
logoIcon.TextColor3 = COR_ROSA_MAIN
logoIcon.Font = Enum.Font.GothamBold
logoIcon.TextSize = 22
logoIcon.Text = "⚡"
logoIcon.Parent = header

local mainTitle = Instance.new("TextLabel")
mainTitle.Position = UDim2.new(0, 60, 0, 10)
mainTitle.Size = UDim2.new(0, 150, 0, 20)
mainTitle.BackgroundTransparency = 1
mainTitle.TextColor3 = COR_TEXTO_MAIN
mainTitle.Font = Enum.Font.GothamBold
mainTitle.TextSize = 14
mainTitle.TextXAlignment = Enum.TextXAlignment.Left
mainTitle.Text = "Main"
mainTitle.Parent = header

local subTitle = Instance.new("TextLabel")
subTitle.Position = UDim2.new(0, 60, 0, 28)
subTitle.Size = UDim2.new(0, 150, 0, 15)
subTitle.BackgroundTransparency = 1
subTitle.TextColor3 = COR_TEXTO_SUB
subTitle.Font = Enum.Font.GothamMedium
subTitle.TextSize = 11
subTitle.TextXAlignment = Enum.TextXAlignment.Left
subTitle.Text = "Core features"
subTitle.Parent = header

-- Botões Decorativos no canto direito (Mover/Fechar)
local headerControls = Instance.new("Frame")
headerControls.Size = UDim2.new(0, 80, 0, 30)
headerControls.Position = UDim2.new(1, -95, 0, 12)
headerControls.BackgroundTransparency = 1
headerControls.Parent = header

local moveIcon = Instance.new("TextLabel")
moveIcon.Size = UDim2.new(0, 30, 1, 0)
moveIcon.BackgroundTransparency = 1
moveIcon.TextColor3 = COR_ROSA_MAIN
moveIcon.Font = Enum.Font.GothamBold
moveIcon.TextSize = 16
moveIcon.Text = "✛"
moveIcon.Parent = headerControls

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 30, 1, 0)
closeBtn.Position = UDim2.new(0, 40, 0, 0)
closeBtn.BackgroundTransparency = 1
closeBtn.TextColor3 = COR_TEXTO_SUB
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 15
closeBtn.Text = "✕"
closeBtn.Parent = headerControls

-- 2. BARRA LATERAL ESQUERDA (TABS)
local sidebar = Instance.new("Frame")
sidebar.Name = "Sidebar"
sidebar.Size = UDim2.new(0, 50, 1, -55)
sidebar.Position = UDim2.new(0, 0, 0, 55)
sidebar.BackgroundColor3 = Color3.fromRGB(14, 13, 16)
sidebar.BorderSizePixel = 0
sidebar.Parent = mainPanel

local function criarAbaBotao(nome, icone, posY, ativo)
	local btn = Instance.new("TextButton")
	btn.Name = nome
	btn.Size = UDim2.new(0, 34, 0, 34)
	btn.Position = UDim2.new(0.5, -17, 0, posY)
	btn.BackgroundColor3 = ativo and Color3.fromRGB(42, 25, 34) or Color3.fromRGB(22, 20, 24)
	btn.TextColor3 = ativo and COR_ROSA_MAIN or COR_TEXTO_SUB
	btn.Font = Enum.Font.GothamBold
	btn.TextSize = 14
	btn.Text = icone
	btn.BorderSizePixel = 0
	btn.Parent = sidebar

	local btnCorner = Instance.new("UICorner")
	btnCorner.CornerRadius = UDim.new(0, 8)
	btnCorner.Parent = btn

	local btnStroke = Instance.new("UIStroke")
	btnStroke.Color = ativo and COR_ROSA_MAIN or Color3.fromRGB(30, 28, 32)
	btnStroke.Thickness = 1
	btnStroke.Parent = btn

	return btn
end

local tabMain = criarAbaBotao("TabMain", "🎯", 15, true)
local tabEsp = criarAbaBotao("TabEsp", "👁️", 55, false)
local tabMisc = criarAbaBotao("TabMisc", "⚙️", 95, false)

-- 3. CONTAINER DE CONTEÚDO (DUAS COLUNAS)
local container = Instance.new("Frame")
container.Name = "Container"
container.Size = UDim2.new(1, -65, 1, -65)
container.Position = UDim2.new(0, 55, 0, 55)
container.BackgroundTransparency = 1
container.Parent = mainPanel

-- Coluna Esquerda: Features Ativas
local leftCol = Instance.new("ScrollingFrame")
leftCol.Name = "LeftColumn"
leftCol.Size = UDim2.new(0.5, -10, 1, 0)
leftCol.Position = UDim2.new(0, 0, 0, 0)
leftCol.BackgroundColor3 = COR_CONTEUDO
leftCol.BorderSizePixel = 0
leftCol.ScrollBarThickness = 3
leftCol.ScrollBarImageColor3 = COR_ROSA_MAIN
leftCol.CanvasSize = UDim2.new(0, 0, 0, 640) -- Aumentado para acomodar o SpinBot
leftCol.Parent = container

local lcCorner = Instance.new("UICorner")
lcCorner.CornerRadius = UDim.new(0, 8)
lcCorner.Parent = leftCol

local lcStroke = Instance.new("UIStroke")
lcStroke.Color = COR_BORDINHA
lcStroke.Thickness = 1
lcStroke.Parent = leftCol

-- Coluna Direita: Configurações Gerais
local rightCol = Instance.new("ScrollingFrame")
rightCol.Name = "RightColumn"
rightCol.Size = UDim2.new(0.5, -10, 1, 0)
rightCol.Position = UDim2.new(0.5, 10, 0, 0)
rightCol.BackgroundColor3 = COR_CONTEUDO
rightCol.BorderSizePixel = 0
rightCol.ScrollBarThickness = 3
rightCol.ScrollBarImageColor3 = COR_ROSA_MAIN
rightCol.CanvasSize = UDim2.new(0, 0, 0, 520) 
rightCol.Parent = container

local rcCorner = Instance.new("UICorner")
rcCorner.CornerRadius = UDim.new(0, 8)
rcCorner.Parent = rightCol

local rcStroke = Instance.new("UIStroke")
rcStroke.Color = COR_BORDINHA
rcStroke.Thickness = 1
rcStroke.Parent = rightCol

--- ==========================================
--- ⚙️ CONFIGURAÇÕES E ESTADO DO SCRIPT
--- ==========================================
local espSkeletonActive = false
local espBoxActive = false
local espLineActive = false 
local espNameActive = false
local fovActive = false
local aimbotActive = false
local silentAimActive = false
local speedActive = false
local autoReloadActive = false
local noclipActive = false
local noSpreadActive = false 
local hitboxActive = false 
local headExpandActive = false 
local spinBotActive = false -- Novo: Estado do SpinBot
local EXPANDIR_TORSO = true 

local speedValue = 50 
local fovRadius = 100
local hitboxSize = 15 
local headSizeValue = 5 
local spinAngle = 0 -- Controle de rotação do SpinBot

local lockedTarget = nil 
local originalSpreadValues = {} 
local originalHitboxes = {} 
local originalHeadSizes = {} 

-- Criando o círculo de FOV
local fovCircle = Drawing.new("Circle")
fovCircle.Visible = false
fovCircle.Color = COR_ROSA_MAIN
fovCircle.Thickness = 1.5
fovCircle.NumSides = 64
fovCircle.Radius = fovRadius
fovCircle.Filled = false
fovCircle.Position = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)

--- ==========================================
--- 🎨 SISTEMA PRECISO DE FILTRO DE TIME
--- ==========================================
local function checarCorJogador(outroPlayer)
	if filtroAtual == "ALL" then return true end

	if outroPlayer.Team then
		local nomeCor = string.lower(outroPlayer.Team.TeamColor.Name)
		if filtroAtual == "ORANGE" then
			return string.find(nomeCor, "orange") or string.find(nomeCor, "apricot") or string.find(nomeCor, "tangerine")
		elseif filtroAtual == "BLUE" then
			return string.find(nomeCor, "blue") or string.find(nomeCor, "teal") or string.find(nomeCor, "cyan")
		elseif filtroAtual == "RED" then
			return string.find(nomeCor, "red") or string.find(nomeCor, "maroon") or string.find(nomeCor, "crimson")
		end
	end

	local corTime = outroPlayer.TeamColor and outroPlayer.TeamColor.Color
	if corTime then
		local r, g, b = corTime.R, corTime.G, corTime.B
		if filtroAtual == "ORANGE" and (r > 0.7 and g > 0.3 and b < 0.3) then
			return true
		elseif filtroAtual == "BLUE" and (b > 0.7 and r < 0.4) then
			return true
		elseif filtroAtual == "RED" and (r > 0.7 and g < 0.3 and b < 0.3) then
			return true
		end
	end

	return false
end

--- ==========================================
--- 👁️ FUNÇÃO WALL CHECK
--- ==========================================
local function estaVisivel(personagemInimigo)
	if not player.Character or not personagemInimigo then return false end
	local head = personagemInimigo:FindFirstChild("Head")
	if not head then return false end

	local origem = camera.CFrame.Position
	local destino = head.Position
	local direcao = (destino - origem)

	local raycastParams = RaycastParams.new()
	raycastParams.FilterType = Enum.RaycastFilterType.Exclude
	raycastParams.FilterDescendantsInstances = {player.Character, camera}
	raycastParams.IgnoreWater = true

	local resultado = workspace:Raycast(origem, direcao, raycastParams)

	if not resultado or resultado.Instance:IsDescendantOf(personagemInimigo) then
		return true
	end
	return false
end

--- ==========================================
--- 🎯 SELEÇÃO DE ALVO NO FOV (STICKY)
--- ==========================================
local function obterJogadorMaisProximoNoFOV()
	if lockedTarget and lockedTarget:FindFirstChild("Head") then
		local hum = lockedTarget:FindFirstChildOfClass("Humanoid")
		if hum and hum.Health > 0 and estaVisivel(lockedTarget) then
			return lockedTarget
		end
	end

	lockedTarget = nil
	local alvoMaisProximo = nil
	local menorDistancia = fovRadius
	local centroDaTela = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)

	for _, outroPlayer in ipairs(Players:GetPlayers()) do
		if outroPlayer ~= player and outroPlayer.Character then
			if checarCorJogador(outroPlayer) then
				local caract = outroPlayer.Character
				local head = caract:FindFirstChild("Head")
				local humanoid = caract:FindFirstChildOfClass("Humanoid")

				if head and humanoid and humanoid.Health > 0 then
					if estaVisivel(caract) then
						local posicaoTela, naTela = camera:WorldToViewportPoint(head.Position)

						if naTela then
							local posicaoInimigo2D = Vector2.new(posicaoTela.X, posicaoTela.Y)
							local distancia2D = (posicaoInimigo2D - centroDaTela).Magnitude

							if distancia2D < menorDistancia then
								menorDistancia = distancia2D
								alvoMaisProximo = caract
							end
						end
					end
				end
			end
		end
	end
	
	lockedTarget = alvoMaisProximo
	return alvoMaisProximo
end

--- ==========================================
--- 💀 LÓGICA DO ESP (SKELETON, BOX 2D, SNAPLINES & NAMES)
--- ==========================================
local cacheEsp = {}

local function criarLinha(cor, espessura)
	local l = Drawing.new("Line")
	l.Visible = false
	l.Color = cor or Color3.fromRGB(255, 0, 0)
	l.Thickness = espessura or 1.5
	l.Transparency = 1
	return l
end

local function criarTexto(cor, playerNome)
	local t = Drawing.new("Text")
	t.Visible = false
	t.Color = cor or Color3.fromRGB(255, 255, 255)
	t.Size = 13
	t.Center = true
	t.Outline = true
	t.OutlineColor = Color3.fromRGB(0, 0, 0)
	t.Text = playerNome
	return t
end

local function adicionarDesenhosEsp(outroPlayer)
	if cacheEsp[outroPlayer] then return end

	local desenhos = {
		Box = {
			Top = criarLinha(COR_ROSA_MAIN),
			Bottom = criarLinha(COR_ROSA_MAIN),
			Left = criarLinha(COR_ROSA_MAIN),
			Right = criarLinha(COR_ROSA_MAIN)
		},
		Line = criarLinha(COR_ROSA_MAIN, 1.2),
		Text = criarTexto(COR_TEXTO_MAIN, outroPlayer.Name),
		Skeleton = {}
	}
	
	for i = 1, 15 do
		table.insert(desenhos.Skeleton, criarLinha(Color3.fromRGB(255, 255, 255), 1.2))
	end

	cacheEsp[outroPlayer] = desenhos
end

local function removerDesenhosEsp(outroPlayer)
	local desenhos = cacheEsp[outroPlayer]
	if desenhos then
		for _, linha in pairs(desenhos.Box) do
			linha:Remove()
		end
		for _, linha in pairs(desenhos.Skeleton) do
			linha:Remove()
		end
		if desenhos.Line then
			desenhos.Line:Remove()
		end
		if desenhos.Text then
			desenhos.Text:Remove()
		end
		cacheEsp[outroPlayer] = nil
	end
end

for _, p in ipairs(Players:GetPlayers()) do
	if p ~= player then
		adicionarDesenhosEsp(p)
	end
end
Players.PlayerAdded:Connect(function(p)
	if p ~= player then
		adicionarDesenhosEsp(p)
	end
end)
Players.PlayerRemoving:Connect(removerDesenhosEsp)

local function atualizarEsp()
	for outroPlayer, desenhos in pairs(cacheEsp) do
		local caract = outroPlayer.Character
		if not caract then
			for _, l in pairs(desenhos.Box) do l.Visible = false end
			for _, l in pairs(desenhos.Skeleton) do l.Visible = false end
			desenhos.Line.Visible = false
			desenhos.Text.Visible = false
			continue
		end

		local humanoid = caract:FindFirstChildOfClass("Humanoid")
		local hrp = caract:FindFirstChild("HumanoidRootPart")
		local head = caract:FindFirstChild("Head")

		if not humanoid or not hrp or not head or humanoid.Health <= 0 or not checarCorJogador(outroPlayer) then
			for _, l in pairs(desenhos.Box) do l.Visible = false end
			for _, l in pairs(desenhos.Skeleton) do l.Visible = false end
			desenhos.Line.Visible = false
			desenhos.Text.Visible = false
			continue
		end

		local posHrp, naTela = camera:WorldToViewportPoint(hrp.Position)
		local corVisual = estaVisivel(caract) and COR_ROSA_MAIN or Color3.fromRGB(150, 40, 70)

		-- ESP LINES (SNAPLINES)
		if espLineActive and naTela then
			desenhos.Line.From = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y)
			desenhos.Line.To = Vector2.new(posHrp.X, posHrp.Y)
			desenhos.Line.Color = corVisual
			desenhos.Line.Visible = true
		else
			desenhos.Line.Visible = false
		end

		-- ESP BOX
		if espBoxActive and naTela then
			local topo, _ = camera:WorldToViewportPoint(head.Position + Vector3.new(0, 1.8, 0))
			local base, _ = camera:WorldToViewportPoint(hrp.Position - Vector3.new(0, 3, 0))
			
			local altura = math.abs(topo.Y - base.Y)
			local largura = altura / 1.5

			local x = posHrp.X - largura / 2
			local y = topo.Y

			desenhos.Box.Top.From = Vector2.new(x, y)
			desenhos.Box.Top.To = Vector2.new(x + largura, y)
			desenhos.Box.Top.Color = corVisual
			desenhos.Box.Top.Visible = true

			desenhos.Box.Bottom.From = Vector2.new(x, y + altura)
			desenhos.Box.Bottom.To = Vector2.new(x + largura, y + altura)
			desenhos.Box.Bottom.Color = corVisual
			desenhos.Box.Bottom.Visible = true

			desenhos.Box.Left.From = Vector2.new(x, y)
			desenhos.Box.Left.To = Vector2.new(x, y + altura)
			desenhos.Box.Left.Color = corVisual
			desenhos.Box.Left.Visible = true

			desenhos.Box.Right.From = Vector2.new(x + largura, y)
			desenhos.Box.Right.To = Vector2.new(x + largura, y + altura)
			desenhos.Box.Right.Color = corVisual
			desenhos.Box.Right.Visible = true
		else
			for _, l in pairs(desenhos.Box) do l.Visible = false end
		end

		-- ESP NAMES
		if espNameActive and naTela then
			local topoCabeca, _ = camera:WorldToViewportPoint(head.Position + Vector3.new(0, 2.2, 0))
			desenhos.Text.Position = Vector2.new(topoCabeca.X, topoCabeca.Y - 15)
			desenhos.Text.Color = corVisual
			desenhos.Text.Visible = true
		else
			desenhos.Text.Visible = false
		end

		-- ESP SKELETON
		if espSkeletonActive and naTela then
			local conexoes = {}
			local isR15 = (humanoid.RigType == Enum.HumanoidRigType.R15)

			if isR15 then
				local parts = {
					Head = head,
					UpperTorso = caract:FindFirstChild("UpperTorso"),
					LowerTorso = caract:FindFirstChild("LowerTorso"),
					LeftUpperArm = caract:FindFirstChild("LeftUpperArm"),
					LeftLowerArm = caract:FindFirstChild("LeftLowerArm"),
					LeftHand = caract:FindFirstChild("LeftHand"),
					RightUpperArm = caract:FindFirstChild("RightUpperArm"),
					RightLowerArm = caract:FindFirstChild("RightLowerArm"),
					RightHand = caract:FindFirstChild("RightHand"),
					LeftUpperLeg = caract:FindFirstChild("LeftUpperLeg"),
					LeftLowerLeg = caract:FindFirstChild("LeftLowerLeg"),
					LeftFoot = caract:FindFirstChild("LeftFoot"),
					RightUpperLeg = caract:FindFirstChild("RightUpperLeg"),
					RightLowerLeg = caract:FindFirstChild("RightLowerLeg"),
					RightFoot = caract:FindFirstChild("RightFoot")
				}

				if parts.UpperTorso and parts.LowerTorso then
					conexoes = {
						{parts.Head, parts.UpperTorso},
						{parts.UpperTorso, parts.LowerTorso},
						{parts.UpperTorso, parts.LeftUpperArm},
						{parts.LeftUpperArm, parts.LeftLowerArm},
						{parts.LeftLowerArm, parts.LeftHand},
						{parts.UpperTorso, parts.RightUpperArm},
						{parts.RightUpperArm, parts.RightLowerArm},
						{parts.RightLowerArm, parts.RightHand},
						{parts.LowerTorso, parts.LeftUpperLeg},
						{parts.LeftUpperLeg, parts.LeftLowerLeg},
						{parts.LeftLowerLeg, parts.LeftFoot},
						{parts.LowerTorso, parts.RightUpperLeg},
						{parts.RightUpperLeg, parts.RightLowerLeg},
						{parts.RightLowerLeg, parts.RightFoot}
					}
				end
			else
				local parts = {
					Head = head,
					Torso = caract:FindFirstChild("Torso"),
					LeftArm = caract:FindFirstChild("Left Arm"),
					RightArm = caract:FindFirstChild("Right Arm"),
					LeftLeg = caract:FindFirstChild("Left Leg"),
					RightLeg = caract:FindFirstChild("Right Leg")
				}

				if parts.Torso then
					conexoes = {
						{parts.Head, parts.Torso},
						{parts.Torso, parts.LeftArm},
						{parts.Torso, parts.RightArm},
						{parts.Torso, parts.LeftLeg},
						{parts.Torso, parts.RightLeg}
					}
				end
			end

			local indiceLinha = 1
			local corSkel = estaVisivel(caract) and COR_ROSA_MAIN or Color3.fromRGB(240, 240, 240)

			for _, conexao in ipairs(conexoes) do
				local partA, partB = conexao[1], conexao[2]
				if partA and partB then
					local pA, naTelaA = camera:WorldToViewportPoint(partA.Position)
					local pB, naTelaB = camera:WorldToViewportPoint(partB.Position)

					if naTelaA and naTelaB then
						local line = desenhos.Skeleton[indiceLinha]
						if line then
							line.From = Vector2.new(pA.X, pA.Y)
							line.To = Vector2.new(pB.X, pB.Y)
							line.Color = corSkel
							line.Visible = true
							indiceLinha = indiceLinha + 1
						end
					end
				end
			end

			for i = indiceLinha, #desenhos.Skeleton do
				desenhos.Skeleton[i].Visible = false
			end
		else
			for _, l in pairs(desenhos.Skeleton) do l.Visible = false end
		end
	end
end

--- ==========================================
--- 💥 LÓGICA DO HITBOX EXPANDER
--- ==========================================
local function restaurarHitboxOriginal()
	for outroPlayer, partes in pairs(originalHitboxes) do
		if outroPlayer.Character then
			for nomeParte, configs in pairs(partes) do
				local parte = outroPlayer.Character:FindFirstChild(nomeParte)
				if parte then
					parte.Size = configs.Size
					parte.Transparency = configs.Transparency
					parte.CanCollide = configs.CanCollide
				end
			end
		end
	end
	table.clear(originalHitboxes)
end

local function processarHitboxes()
	if not hitboxActive then return end
	
	for _, outroPlayer in ipairs(Players:GetPlayers()) do
		if outroPlayer ~= player and outroPlayer.Character then
			local character = outroPlayer.Character
			local humanoid = character:FindFirstChildOfClass("Humanoid")
			
			if humanoid and humanoid.Health > 0 and checarCorJogador(outroPlayer) then
				local partesParaExpandir = {}
				
				local hrp = character:FindFirstChild("HumanoidRootPart")
				if hrp then table.insert(partesParaExpandir, hrp) end
				
				if EXPANDIR_TORSO then
					local torso = character:FindFirstChild("Torso")
					if torso then table.insert(partesParaExpandir, torso) end
					
					local upperTorso = character:FindFirstChild("UpperTorso")
					local lowerTorso = character:FindFirstChild("LowerTorso")
					if upperTorso then table.insert(partesParaExpandir, upperTorso) end
					if lowerTorso then table.insert(partesParaExpandir, lowerTorso) end
				end

				if not originalHitboxes[outroPlayer] then
					originalHitboxes[outroPlayer] = {}
				end

				for _, parte in ipairs(partesParaExpandir) do
					if not originalHitboxes[outroPlayer][parte.Name] then
						originalHitboxes[outroPlayer][parte.Name] = {
							Size = parte.Size,
							Transparency = parte.Transparency,
							CanCollide = parte.CanCollide
						}
					end
					
					parte.Size = Vector3.new(hitboxSize, hitboxSize, hitboxSize)
					parte.Transparency = 0.7 
					parte.CanCollide = false 
				end
			end
		end
	end
end

--- ==========================================
--- 👤 LÓGICA DO BIG HEAD EXPANDER
--- ==========================================
local function restaurarHeadOriginal()
	for outroPlayer, tamanhoOriginal in pairs(originalHeadSizes) do
		if outroPlayer.Character then
			local head = outroPlayer.Character:FindFirstChild("Head")
			if head then
				head.Size = tamanhoOriginal
			end
		end
	end
	table.clear(originalHeadSizes)
end

local function processarBigHead()
	if not headExpandActive then return end

	for _, outroPlayer in ipairs(Players:GetPlayers()) do
		if outroPlayer ~= player and outroPlayer.Character then
			local character = outroPlayer.Character
			local head = character:FindFirstChild("Head")
			local humanoid = character:FindFirstChildOfClass("Humanoid")

			if head and humanoid and humanoid.Health > 0 and checarCorJogador(outroPlayer) then
				if not originalHeadSizes[outroPlayer] then
					originalHeadSizes[outroPlayer] = head.Size
				end
				head.Size = Vector3.new(headSizeValue, headSizeValue, headSizeValue)
			end
		end
	end
end

--- ==========================================
--- 🌀 LÓGICA DO SPINBOT (DESYNC / ANTI-AIM)
--- ==========================================
local function processarSpinBot()
	if not spinBotActive then return end
	local char = player.Character
	if not char then return end

	local hrp = char:FindFirstChild("HumanoidRootPart")
	if hrp then
		spinAngle = (spinAngle + 45) % 360
		-- Altera apenas a rotação da RootPart sem mexer na Câmera local
		hrp.CFrame = CFrame.new(hrp.Position) * CFrame.Angles(0, math.rad(spinAngle), 0)
	end
end

--- ==========================================
--- ⚡ HOOKMETAMETHOD SILENT AIM
--- ==========================================
local mt = getrawmetatable(game)
local oldNamecall = mt.__namecall
local oldIndex = mt.__index
setreadonly(mt, false)

mt.__index = newcclosure(function(self, idx)
	if keyAprovada and silentAimActive and self == mouse and not checkcaller() then
		local alvo = obterJogadorMaisProximoNoFOV()
		if alvo then
			local targetPart = alvo:FindFirstChild("Head") or alvo:FindFirstChild("HumanoidRootPart")
			if targetPart then
				if idx == "Hit" then
					return targetPart.CFrame
				elseif idx == "Target" then
					return targetPart
				end
			end
		end
	end
	return oldIndex(self, idx)
end)

setreadonly(mt, true)

--- ==========================================
--- 🛠️ SEÇÃO DE ELEMENTOS DA UI (ESTILO LUV-U)
--- ==========================================

-- Função para criar Checkbox Modernas
local function criarCheckbox(parent, texto, posY)
	local containerFrame = Instance.new("Frame")
	containerFrame.Size = UDim2.new(1, -20, 0, 30)
	containerFrame.Position = UDim2.new(0, 10, 0, posY)
	containerFrame.BackgroundTransparency = 1
	containerFrame.Parent = parent

	local box = Instance.new("TextButton")
	box.Size = UDim2.new(0, 16, 0, 16)
	box.Position = UDim2.new(0, 5, 0.5, -8)
	box.BackgroundColor3 = Color3.fromRGB(26, 24, 29)
	box.Text = ""
	box.BorderSizePixel = 0
	box.Parent = containerFrame

	local boxCorner = Instance.new("UICorner")
	boxCorner.CornerRadius = UDim.new(0, 4)
	boxCorner.Parent = box

	local boxStroke = Instance.new("UIStroke")
	boxStroke.Color = COR_BORDINHA
	boxStroke.Thickness = 1
	boxStroke.Parent = box

	local innerDot = Instance.new("Frame")
	innerDot.Size = UDim2.new(0, 8, 0, 8)
	innerDot.Position = UDim2.new(0.5, -4, 0.5, -4)
	innerDot.BackgroundColor3 = COR_ROSA_MAIN
	innerDot.BorderSizePixel = 0
	innerDot.Visible = false
	innerDot.Parent = box

	local dotCorner = Instance.new("UICorner")
	dotCorner.CornerRadius = UDim.new(1, 0)
	dotCorner.Parent = innerDot

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, -30, 1, 0)
	label.Position = UDim2.new(0, 30, 0, 0)
	label.BackgroundTransparency = 1
	label.TextColor3 = COR_TEXTO_MAIN
	label.Font = Enum.Font.GothamMedium
	label.TextSize = 12
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Text = texto
	label.Parent = containerFrame

	return box, innerDot
end

-- Seção Título dentro das colunas
local function criarSecaoTitulo(parent, texto, posY)
	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, -20, 0, 25)
	label.Position = UDim2.new(0, 10, 0, posY)
	label.BackgroundTransparency = 1
	label.TextColor3 = COR_ROSA_MAIN
	label.Font = Enum.Font.GothamBold
	label.TextSize = 13
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Text = "🔘 " .. texto
	label.Parent = parent
end

-- 1. ADICIONANDO BOTÕES DA COLUNA ESQUERDA (FEATURES)
criarSecaoTitulo(leftCol, "Aiming & Combat", 10)
local cbAimbot, dotAimbot = criarCheckbox(leftCol, "Aimbot Head", 40)
local cbSilent, dotSilent = criarCheckbox(leftCol, "Silent Aim", 75)
local cbNoSpread, dotNoSpread = criarCheckbox(leftCol, "No Spread", 110)
local cbAutoReload, dotAutoReload = criarCheckbox(leftCol, "Auto Reload", 145)

criarSecaoTitulo(leftCol, "Visuals (ESP)", 190)
local cbEspSkel, dotEspSkel = criarCheckbox(leftCol, "ESP Skeleton", 220)
local cbEspBox, dotEspBox = criarCheckbox(leftCol, "ESP Box", 255)
local cbEspLine, dotEspLine = criarCheckbox(leftCol, "ESP Snaplines", 290)
local cbEspName, dotEspName = criarCheckbox(leftCol, "ESP Names (Nomes)", 325)
local cbFov, dotFov = criarCheckbox(leftCol, "Visualizar FOV", 360) 

criarSecaoTitulo(leftCol, "Movement & Others", 405) 
local cbNoclip, dotNoclip = criarCheckbox(leftCol, "Noclip Inteligente", 435)
local cbHitbox, dotHitbox = criarCheckbox(leftCol, "Hitbox Expander", 470)
local cbSpeed, dotSpeed = criarCheckbox(leftCol, "Speed Hack", 505)
local cbHead, dotHead = criarCheckbox(leftCol, "Big Head (Cabeção)", 540) 
local cbSpinBot, dotSpinBot = criarCheckbox(leftCol, "SpinBot (Server Only)", 575) -- Novo: Checkbox do SpinBot

-- 2. ADICIONANDO CONTROLES DA COLUNA DIREITA (SETTINGS & SLIDERS)
criarSecaoTitulo(rightCol, "Target Filters", 10)

-- Seletor de Filtro de Cor
local btnFiltroColor = Instance.new("TextButton")
btnFiltroColor.Size = UDim2.new(1, -20, 0, 32)
btnFiltroColor.Position = UDim2.new(0, 10, 0, 40)
btnFiltroColor.BackgroundColor3 = COR_FUNDO
btnFiltroColor.TextColor3 = COR_TEXTO_MAIN
btnFiltroColor.Font = Enum.Font.GothamBold
btnFiltroColor.TextSize = 12
btnFiltroColor.Text = "Filtro de Cor: TODOS"
btnFiltroColor.Parent = rightCol

local bfcCorner = Instance.new("UICorner")
bfcCorner.CornerRadius = UDim.new(0, 6)
bfcCorner.Parent = btnFiltroColor

local bfcStroke = Instance.new("UIStroke")
bfcStroke.Color = COR_BORDINHA
bfcStroke.Thickness = 1
bfcStroke.Parent = btnFiltroColor

btnFiltroColor.MouseButton1Click:Connect(function()
	if not keyAprovada then return end
	if filtroAtual == "ALL" then
		filtroAtual = "ORANGE"
	elseif filtroAtual == "ORANGE" then
		filtroAtual = "BLUE"
	elseif filtroAtual == "BLUE" then
		filtroAtual = "RED"
	else
		filtroAtual = "ALL"
	end

	local configs = CORES_FILTRO[filtroAtual]
	btnFiltroColor.Text = "Filtro de Cor: " .. string.upper(configs.Nome)
	TweenService:Create(btnFiltroColor, TweenInfo.new(0.2), {TextColor3 = configs.Cor}):Play()
end)

-- Função para criar Sliders
local function criarSlider(parent, texto, posY, min, max, padrao, callback)
	local containerFrame = Instance.new("Frame")
	containerFrame.Size = UDim2.new(1, -20, 0, 45)
	containerFrame.Position = UDim2.new(0, 10, 0, posY)
	containerFrame.BackgroundTransparency = 1
	containerFrame.Parent = parent

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, 0, 0, 15)
	label.BackgroundTransparency = 1
	label.TextColor3 = COR_TEXTO_SUB
	label.Font = Enum.Font.GothamMedium
	label.TextSize = 11
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Text = texto .. ": " .. padrao
	label.Parent = containerFrame

	local sliderFrame = Instance.new("Frame")
	sliderFrame.Size = UDim2.new(1, 0, 0, 6)
	sliderFrame.Position = UDim2.new(0, 0, 0, 25)
	sliderFrame.BackgroundColor3 = Color3.fromRGB(35, 33, 40)
	sliderFrame.BorderSizePixel = 0
	sliderFrame.Parent = containerFrame

	local sfCorner = Instance.new("UICorner")
	sfCorner.CornerRadius = UDim.new(1, 0)
	sfCorner.Parent = sliderFrame

	local pctPadrao = (padrao - min) / (max - min)

	local sliderProgress = Instance.new("Frame")
	sliderProgress.Size = UDim2.new(pctPadrao, 0, 1, 0)
	sliderProgress.BackgroundColor3 = COR_ROSA_MAIN
	sliderProgress.BorderSizePixel = 0
	sliderProgress.Parent = sliderFrame

	local spCorner = Instance.new("UICorner")
	spCorner.CornerRadius = UDim.new(1, 0)
	spCorner.Parent = sliderProgress

	local sliderButton = Instance.new("TextButton")
	sliderButton.Size = UDim2.new(0, 12, 0, 12)
	sliderButton.Position = UDim2.new(pctPadrao, -6, 0.5, -6)
	sliderButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
	sliderButton.Text = ""
	sliderButton.BorderSizePixel = 0
	sliderButton.Parent = sliderFrame

	local sbCorner = Instance.new("UICorner")
	sbCorner.CornerRadius = UDim.new(1, 0)
	sbCorner.Parent = sliderButton

	local dragging = false

	local function updateSlider(input)
		local mouseX = input.Position.X
		local sliderX = sliderFrame.AbsolutePosition.X
		local sliderWidth = sliderFrame.AbsoluteSize.X
		
		local percentage = math.clamp((mouseX - sliderX) / sliderWidth, 0, 1)
		
		sliderButton.Position = UDim2.new(percentage, -6, 0.5, -6)
		sliderProgress.Size = UDim2.new(percentage, 0, 1, 0)
		
		local valorCalculado = math.floor(min + (percentage * (max - min)))
		label.Text = texto .. ": " .. valorCalculado
		callback(valorCalculado)
	end

	sliderButton.InputBegan:Connect(function(input)
		if not keyAprovada then return end
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
		end
	end)

	UserInputService.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = false
		end
	end)

	UserInputService.InputChanged:Connect(function(input)
		if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
			updateSlider(input)
		end
	end)
end

criarSecaoTitulo(rightCol, "Adjustments", 90)
criarSlider(rightCol, "Tamanho FOV", 120, 10, 300, 100, function(v) fovRadius = v end)
criarSlider(rightCol, "Velocidade Correr", 180, 16, 250, 50, function(v) speedValue = v end)
criarSlider(rightCol, "Tamanho Hitbox", 240, 2, 50, 15, function(v) hitboxSize = v end)
criarSlider(rightCol, "Tamanho da Cabeça", 300, 1, 15, 5, function(v) headSizeValue = v end)

-- Ativadores Estéticos das Checkboxes
local function ligarCheckbox(cb, dot, refValue, onToggle)
	cb.MouseButton1Click:Connect(function()
		if not keyAprovada then return end
		if cb:FindFirstChild("Frame") then
			cb.Frame.Visible = not cb.Frame.Visible
			onToggle(cb.Frame.Visible)
		else
			dot.Visible = not dot.Visible
			onToggle(dot.Visible)
		end
	end)
end

ligarCheckbox(cbAimbot, dotAimbot, aimbotActive, function(state)
	aimbotActive = state
	if aimbotActive and silentAimActive then
		silentAimActive = false
		dotSilent.Visible = false
	end
end)

ligarCheckbox(cbSilent, dotSilent, silentAimActive, function(state)
	silentAimActive = state
	if silentAimActive and aimbotActive then
		aimbotActive = false
		dotAimbot.Visible = false
	end
end)

ligarCheckbox(cbNoSpread, dotNoSpread, noSpreadActive, function(state)
	noSpreadActive = state
	if not noSpreadActive then
		for objeto, valor in pairs(originalSpreadValues) do
			if objeto and objeto.Parent then objeto.Value = valor end
		end
		table.clear(originalSpreadValues)
	end
end)

ligarCheckbox(cbAutoReload, dotAutoReload, autoReloadActive, function(state) autoReloadActive = state end)
ligarCheckbox(cbEspSkel, dotEspSkel, espSkeletonActive, function(state) espSkeletonActive = state end)
ligarCheckbox(cbEspBox, dotEspBox, espBoxActive, function(state) espBoxActive = state end)
ligarCheckbox(cbEspLine, dotEspLine, espLineActive, function(state) espLineActive = state end)
ligarCheckbox(cbEspName, dotEspName, espNameActive, function(state) espNameActive = state end)

ligarCheckbox(cbFov, dotFov, fovActive, function(state)
	fovActive = state
	fovCircle.Visible = state
end)

ligarCheckbox(cbNoclip, dotNoclip, noclipActive, function(state) noclipActive = state end)

ligarCheckbox(cbHitbox, dotHitbox, hitboxActive, function(state)
	hitboxActive = state
	if not hitboxActive then
		restaurarHitboxOriginal()
	end
end)

ligarCheckbox(cbSpeed, dotSpeed, speedActive, function(state)
	speedActive = state
	if not speedActive then
		local char = player.Character
		local hum = char and char:FindFirstChildOfClass("Humanoid")
		if hum then hum.WalkSpeed = 16 end
	end
end)

ligarCheckbox(cbHead, dotHead, headExpandActive, function(state)
	headExpandActive = state
	if not headExpandActive then
		restaurarHeadOriginal()
	end
end)

ligarCheckbox(cbSpinBot, dotSpinBot, spinBotActive, function(state) spinBotActive = state end) -- Ativador do SpinBot

--- ==========================================
--- 🔄 MECÂNICAS DO JOGO E LOOPS
--- ==========================================
local ultimoTempoRecarga = 0

local function verificarAutoReload()
	if not autoReloadActive then return end
	if os.clock() - ultimoTempoRecarga < 1.5 then return end

	local char = player.Character
	if not char then return end

	local armaEquipada = char:FindFirstChildOfClass("Tool")
	if armaEquipada then
		local ammo = armaEquipada:FindFirstChild("Ammo") or armaEquipada:FindFirstChild("Clip") or armaEquipada:FindFirstChild("CurrentAmmo")
		if ammo and ammo:IsA("ValueBase") and ammo.Value == 0 then
			ultimoTempoRecarga = os.clock()
			VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.R, false, game)
			task.wait(0.05)
			VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.R, false, game)
		end
	end
end

local function processarNoclip()
	if not noclipActive then return end
	local char = player.Character
	if not char then return end
	local hrp = char:FindFirstChild("HumanoidRootPart")
	if not hrp then return end

	local tamanhoCaixa = Vector3.new(3.5, 5, 3.5)
	local cframeCaixa = hrp.CFrame * CFrame.new(0, 0.4, 0)

	local params = OverlapParams.new()
	params.FilterType = Enum.RaycastFilterType.Exclude
	params.FilterDescendantsInstances = {char}

	local partesProximas = workspace:GetPartBoundsInBox(cframeCaixa, tamanhoCaixa, params)

	for _, parte in ipairs(partesProximas) do
		if parte:IsA("BasePart") and parte.CanCollide then
			local diferencaY = hrp.Position.Y - parte.Position.Y
			if diferencaY < 2.6 then 
				parte.CanCollide = false
				task.delay(0.1, function()
					if parte and parte.Parent then parte.CanCollide = true end
				end)
			end
		end
	end
end

local function processarNoSpread()
	if not noSpreadActive then return end
	local char = player.Character
	if not char then return end
	local tool = char:FindFirstChildOfClass("Tool")
	if not tool then return end

	for _, desc in ipairs(tool:GetDescendants()) do
		if desc:IsA("NumberValue") or desc:IsA("IntValue") then
			local nomeLower = string.lower(desc.Name)
			if string.find(nomeLower, "spread") or string.find(nomeLower, "recoil") or string.find(nomeLower, "accuracy") or string.find(nomeLower, "dispersion") or string.find(nomeLower, "inaccuracy") or string.find(nomeLower, "aimcone") then
				if not originalSpreadValues[desc] then
					originalSpreadValues[desc] = desc.Value
				end
				desc.Value = 0
			end
		end
	end
end

RunService.RenderStepped:Connect(function()
	if not keyAprovada then return end

	if fovActive then
		fovCircle.Position = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
		fovCircle.Radius = fovRadius
	end

	atualizarEsp()

	if hitboxActive then
		processarHitboxes()
	else
		restaurarHitboxOriginal()
	end

	if headExpandActive then 
		processarBigHead()
	else
		restaurarHeadOriginal()
	end

	if aimbotActive and not silentAimActive then
		local alvo = obterJogadorMaisProximoNoFOV()
		if alvo and alvo:FindFirstChild("Head") then
			camera.CFrame = camera.CFrame:Lerp(CFrame.new(camera.CFrame.Position, alvo.Head.Position), 0.25)
		end
	end

	verificarAutoReload()
	processarNoclip()
	processarNoSpread()
	processarSpinBot() -- Executa o SpinBot em tempo real

	if speedActive then
		local char = player.Character
		if char then
			local hum = char:FindFirstChildOfClass("Humanoid")
			if hum and hum.WalkSpeed ~= speedValue then hum.WalkSpeed = speedValue end
		end
	end
end)

--- ==========================================
--- 🎬 SISTEMA DE ANIMAÇÃO DO PAINEL
--- ==========================================
local isAnimating = false

local function togglePanel(show)
	if isAnimating or not keyAprovada then return end
	isAnimating = true
	
	local targetSize = show and UDim2.new(0, 650, 0, 360) or UDim2.new(0, 650, 0, 0)
	local tweenInfo = TweenInfo.new(0.35, Enum.EasingStyle.Quart, Enum.EasingDirection.Out)
	
	if show then
		mainPanel.Size = UDim2.new(0, 650, 0, 0)
		mainPanel.Visible = true
		container.Visible = true
	end
	
	local tween = TweenService:Create(mainPanel, tweenInfo, {Size = targetSize})
	tween:Play()
	
	tween.Completed:Connect(function()
		if not show then mainPanel.Visible = false end
		isAnimating = false
	end)
end

openButton.MouseButton1Click:Connect(function()
	if not mainPanel.Visible then togglePanel(true) end
end)

closeBtn.MouseButton1Click:Connect(function()
	togglePanel(false)
end)

--- ==========================================
--- 🔑 DRAGGING SISTEMA (ARRASTAR)
--- ==========================================
local dragging, dragInput, dragStart, startPos
header.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = mainPanel.Position
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then dragging = false end
		end)
	end
end)

header.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
		dragInput = input
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if input == dragInput and dragging then
		local delta = input.Position - dragStart
		mainPanel.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

--- ==========================================
--- 🔑 VERIFICAÇÃO DA KEY
--- ==========================================
keyVerifyBtn.MouseButton1Click:Connect(function()
	if keyTextBox.Text == KEY_CORRETA then
		keyAprovada = true
		erroLabel.TextColor3 = COR_ROSA_MAIN
		erroLabel.Text = "Acesso Permitido! Carregando..."
		task.wait(1)
		
		local sumirTween = TweenService:Create(keyPanel, TweenInfo.new(0.3), {BackgroundTransparency = 1})
		sumirTween:Play()
		keyTextBox.Visible = false
		keyVerifyBtn.Visible = false
		keyTitle.Visible = false
		erroLabel.Visible = false
		
		sumirTween.Completed:Connect(function()
			keyPanel:Destroy()
			openButton.Visible = true
			togglePanel(true)
		end)
	else
		erroLabel.Text = "Key incorreta! Tente novamente."
		keyTextBox.Text = ""
	end
end)
