lua
-- ServerScript - AutoFarm
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local autoFarmEvent = Instance.new("RemoteEvent")
autoFarmEvent.Name = "AutoFarmEvent"
autoFarmEvent.Parent = ReplicatedStorage

local playerAutoFarm = {}  -- Tabela para controlar o estado do auto farm por jogador
local farmInterval = 2  -- Intervalo entre as ações de coleta (em segundos)

-- Função para ativar ou desativar o Auto Farm
local function toggleAutoFarm(player)
    if playerAutoFarm[player.UserId] == nil then
        playerAutoFarm[player.UserId] = false  -- Inicializando estado do jogador
    end
    
    -- Alterna o estado de ativação do Auto Farm
    playerAutoFarm[player.UserId] = not playerAutoFarm[player.UserId]
    return playerAutoFarm[player.UserId]
end

-- Função para realizar a coleta automática
local function startAutoFarm(player)
    while playerAutoFarm[player.UserId] do
        -- Buscar peças (vamos assumir que elas têm o nome "Peça")
        local pieces = workspace:FindPartsInRegion3(workspace.Baseplate.Position, Vector3.new(50, 10, 50), nil)
        
        for _, piece in pairs(pieces) do
            if piece.Name == "Peça" then
                -- Simula a coleta da peça (exemplo: adiciona moedas ou recursos)
                print(player.Name .. " coletou uma peça!")
                piece:Destroy()  -- Destroi a peça após ser coletada
                -- Adicione o recurso ao jogador (exemplo: moedas)
                player.leaderstats.Moedas.Value = player.leaderstats.Moedas.Value + 1
            end
        end
        
        wait(farmInterval)  -- Espera o tempo de intervalo antes de buscar mais peças
    end
end

-- Escuta os comandos dos jogadores
autoFarmEvent.OnServerEvent:Connect(function(player, command)
    if command == "ativar" then
        if not playerAutoFarm[player.UserId] then
            if toggleAutoFarm(player) then
                startAutoFarm(player)  -- Inicia o auto farm
                print(player.Name .. " ativou o Auto Farm!")
            end
        end
    elseif command == "desativar" then
        if playerAutoFarm[player.UserId] then
            if toggleAutoFarm(player) then
                print(player.Name .. " desativou o Auto Farm!")
            end
        end
    end
end)
