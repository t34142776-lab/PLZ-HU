--// Plz Hub - ESP com Team Check
--// Para fins de teste e detecção de ESPs em ambientes autorizados

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Tabela que guardará os objetos ESP
local ESP = {}

-- Função para criar o texto do ESP
local function CreateESP(player)
    if player == LocalPlayer then return end
    if ESP[player] then return end

    local text = Drawing.new("Text")
    text.Visible = false
    text.Center = true
    text.Outline = true
    text.Font = 2
    text.Size = 14
    text.Color = Color3.fromRGB(255, 0, 0)

    ESP[player] = text
end

-- Função para remover o ESP de um player
local function RemoveESP(player)
    if ESP[player] then
        ESP[player]:Remove()
        ESP[player] = nil
    end
end

-- Atualizar ESP constantemente
RunService.RenderStepped:Connect(function()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            -- Team Check
            if player.Team ~= LocalPlayer.Team then
                CreateESP(player)

                local HRP = player.Character.HumanoidRootPart
                local pos, onScreen = Camera:WorldToViewportPoint(HRP.Position)

                local esp = ESP[player]
                if onScreen then
                    esp.Position = Vector2.new(pos.X, pos.Y - 20)
                    esp.Text = player.Name
                    esp.Visible = true
                else
                    esp.Visible = false
                end
            else
                if ESP[player] then
                    ESP[player].Visible = false
                end
            end
        else
            if ESP[player] then
                ESP[player].Visible = false
            end
        end
    end
end)

-- Eventos para novos players
Players.PlayerAdded:Connect(CreateESP)
Players.PlayerRemoving:Connect(RemoveESP)

-- Inicializar ESP para players existentes
for _, player in pairs(Players:GetPlayers()) do
    CreateESP(player)
end

print("[Plz Hub] ESP com Team Check iniciado com sucesso.")
