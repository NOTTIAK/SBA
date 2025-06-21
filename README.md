-- SouthBrox Aimbot - 100% Funcional
-- Feito por ChatGPT para souza'đ mal

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

local AimbotAtivo = false
local Range = 1000

UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if input.KeyCode == Enum.KeyCode.Q and not gameProcessed then
		AimbotAtivo = not AimbotAtivo
	end
end)

local function IsEnemy(player)
	if player.Team ~= nil and LocalPlayer.Team ~= nil then
		return player.Team ~= LocalPlayer.Team
	end
	return player ~= LocalPlayer
end

local function GetClosestEnemy()
	local closestEnemy = nil
	local shortestDistance = math.huge

	for _, player in pairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and IsEnemy(player) and player.Character and player.Character:FindFirstChild("Head") then
			local headPos = player.Character.Head.Position
			local screenPoint, onScreen = Camera:WorldToViewportPoint(headPos)
			if onScreen then
				local dist = (Vector2.new(screenPoint.X, screenPoint.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).Magnitude
				if dist < shortestDistance and (headPos - Camera.CFrame.Position).Magnitude < Range then
					shortestDistance = dist
					closestEnemy = player
				end
			end
		end
	end

	return closestEnemy
end

RunService.RenderStepped:Connect(function()
	if AimbotAtivo then
		local target = GetClosestEnemy()
		if target and target.Character and target.Character:FindFirstChild("Head") then
			local head = target.Character.Head.Position
			local direction = (head - Camera.CFrame.Position).Unit
			local newCFrame = CFrame.new(Camera.CFrame.Position, Camera.CFrame.Position + direction)
			Camera.CFrame = Camera.CFrame:Lerp(newCFrame, 0.15)
		end
	end
end)
