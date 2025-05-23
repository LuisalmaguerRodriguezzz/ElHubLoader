
-- Cargar dependencias
loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Adonis-Admin-Anti-Crash-18757"))()

local Rayfield = loadstring(game:HttpGet("https://sirius.menu/rayfield"))()

local Window = Rayfield:CreateWindow({
    Name = "El Hub",
    Icon = 0,
    LoadingTitle = "El Hub",
    LoadingSubtitle = "by theyfwluis.",
    Theme = "Green",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = nil,
        FileName = "Big Hub"
    },
    KeySystem = false
})

-- Sección de duplicación de cartas
local CardTab = Window:CreateTab("Card", 4483362458)
local CardSection = CardTab:CreateSection("Card Duping")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local VirtualInputManager = game:GetService("VirtualInputManager")
local RunService = game:GetService("RunService")
local StarterGui = game:GetService("StarterGui")
local player = Players.LocalPlayer

local dupeAmount = 10

-- Función para notificar la cantidad de duplicados
local function notifyFarmed(finalAmount)
    Rayfield:Notify({
        Title = "Success",
        Content = "You have Duped " .. tostring(finalAmount) .. " cards and laptops.",
        Duration = 6
    })
end

CardTab:CreateInput({
    Name = "Amount",
    PlaceholderText = "Input",
    RemoveTextAfterFocusLost = false,
    Flag = "DupeAmount",
    Callback = function(value)
        dupeAmount = tonumber(value) or 10
        if dupeAmount <= 0 then
            dupeAmount = 10
        end
    end
})

-- Función de duplicación de cartas y laptops
local function duplicateCardsAndLaptops()
    if dupeAmount <= 0 then return end

    StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.All, false)
    RunService:Set3dRenderingEnabled(false)

    -- Interacción con ClickDetectors
    fireclickdetector(game.Workspace["Streetz War"].Anonymous.ClickDetector)
    task.wait()
    
    player.PlayerGui:WaitForChild("DealerGui")
    local shopGui = player.PlayerGui.DealerGui.ShopFrame
    shopGui.Visible = true
    player.PlayerGui.DealerGui.Frame.Visible = false

    repeat task.wait() until player.Character and player.Character:FindFirstChild("HumanoidRootPart")
    player.Character.HumanoidRootPart.CFrame = CFrame.new(-55, 4.5, 170)
    task.wait(0.5)

    -- Simular clics para duplicar cartas y laptops
    local cardButton = shopGui["Blank Card"]
    local laptopButton = shopGui["laptop"]

    for i = 1, dupeAmount do
        task.wait()
        if cardButton.Visible then
            local cardPos = cardButton.AbsolutePosition
            VirtualInputManager:SendMouseButtonEvent(cardPos.X + 150, cardPos.Y + 60, 0, true, game, 0)
            task.wait()
            VirtualInputManager:SendMouseButtonEvent(cardPos.X + 150, cardPos.Y + 60, 0, false, game, 0)
        end

        if laptopButton.Visible then
            local laptopPos = laptopButton.AbsolutePosition
            VirtualInputManager:SendMouseButtonEvent(laptopPos.X + 150, laptopPos.Y + 60, 0, true, game, 0)
            task.wait()
            VirtualInputManager:SendMouseButtonEvent(laptopPos.X + 150, laptopPos.Y + 60, 0, false, game, 0)
        end
    end

    RunService:Set3dRenderingEnabled(true)

    local exitButton = shopGui.exit
    VirtualInputManager:SendMouseButtonEvent(exitButton.AbsolutePosition.X + 300, exitButton.AbsolutePosition.Y + 65, 0, true, game, 0)
    task.wait()
    VirtualInputManager:SendMouseButtonEvent(exitButton.AbsolutePosition.X + 300, exitButton.AbsolutePosition.Y + 65, 0, false, game, 0)

    player.Character.HumanoidRootPart.CFrame = CFrame.new(949, 4, -102)
    task.wait(2)

    -- Recolección de laptops y cartas duplicadas
    local laptopCount = 0
    for _, v in pairs(player.Backpack:GetChildren()) do
        if v.Name == "Laptop" then
            laptopCount = laptopCount + 1
        end
    end

    for i = 1, laptopCount - 1 do
        spawn(function()
            local args = { true, "NEW123" }
            ReplicatedStorage.Assets.Other.GiverPunchmade:InvokeServer(unpack(args))
        end)
    end

    task.wait(4)
    if player.Backpack:FindFirstChild("Laptop") then
        player.Backpack.Laptop.Parent = player.Character
    end
    task.wait(4)

    local cardCount = 0
    for _, v in pairs(player.Backpack:GetChildren()) do
        if v.Name == "Loaded Card" then
            cardCount = cardCount + 1
        end
    end

    for i = 1, cardCount do
        spawn(function()
            local args = { false, "NEW123" }
            ReplicatedStorage.Assets.Other.GiverPunchmade:InvokeServer(unpack(args))
        end)
    end

    task.wait(1)
    player.Character.Humanoid:UnequipTools()

    StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.All, true)
    notifyFarmed(dupeAmount)
end

CardTab:CreateButton({
    Name = "Duplicate",
    Callback = function()
        duplicateCardsAndLaptops()
    end
})

-- Función de Teletransportación
local TeleportsTab = Window:CreateTab("Teleports", 4483362458)
local TeleportsSection = TeleportsTab:CreateSection("Teleports")
local teleportLocations = {
    { Name = "Mop Job", Position = CFrame.new(-101, 4, 18) },
    { Name = "Box Job", Position = CFrame.new(-118, 4, 300) },
    { Name = "Pizza Job", Position = CFrame.new(166, 5, 49) },
    { Name = "Thrift Job", Position = CFrame.new(-8, 4, 17) }
}

local locationNames = {}
for _, location in ipairs(teleportLocations) do
    table.insert(locationNames, location.Name)
end

local DropdownTeleport = TeleportsTab:CreateDropdown({
    Name = "Jobs",
    Options = locationNames,
    CurrentOption = { locationNames[1] },
    MultipleOptions = false,
    Flag = "TeleportLocationDropdown",
    Callback = function(Options)
        for _, location in ipairs(teleportLocations) do
            if location.Name == Options[1] then
                local player = game.Players.LocalPlayer
                if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                    player.Character.HumanoidRootPart.CFrame = location.Position
                end
                break
            end
        end
    end
})

local PlayerTab = Window:CreateTab("Player", 4483362458) -- Title, Image
local PlayerSection = PlayerTab:CreateSection("Speed")

local WalkSpeedEnabled = false
local defaultWalkSpeed = 16
local currentWalkSpeed = 16

-- Create the toggle to enable/disable custom walk speed
local WalkSpeedToggle = PlayerTab:CreateToggle({
    Name = "Enable Walk Speed",
    Callback = function()
        WalkSpeedEnabled = not WalkSpeedEnabled

        local character = game.Players.LocalPlayer.Character
        if character and character:FindFirstChild("Humanoid") then
            local humanoid = character.Humanoid
            if WalkSpeedEnabled then
                print("Custom Walk Speed Enabled")
                humanoid.WalkSpeed = currentWalkSpeed
            else
                print("Custom Walk Speed Disabled")
                humanoid.WalkSpeed = defaultWalkSpeed
            end
        end
    end
})

local Toggle = PlayerTab:CreateToggle({
    Name = "Disable Camera Shake",
    CurrentValue = false,
    Flag = "DisableCameraShake",
    Callback = function(state)
        local char = game:GetService("Players").LocalPlayer.Character
        if char and char:FindFirstChild("CharacterScripts") then
            char.CharacterScripts.Enabled = not state
            cameraShakeDisabled = state
        end
    end
})

-- Create the slider for adjusting walk speed
local WalkSpeedSlider = PlayerTab:CreateSlider({
    Name = "Walk Speed (0-300)",
    Range = {0, 300},
    Increment = 1,
    Suffix = "Unit",
    CurrentValue = currentWalkSpeed,
    Flag = "Slider2", -- Make sure this flag is unique
    Callback = function(Value)
        currentWalkSpeed = Value
        if WalkSpeedEnabled then
            local character = game.Players.LocalPlayer.Character
            if character and character:FindFirstChild("Humanoid") then
                character.Humanoid.WalkSpeed = currentWalkSpeed
            end
        end
    end,
})


local PlayerSection = PlayerTab:CreateSection("Jump Power")

local JumpPowerEnabled = false
local defaultJumpPower = 50  -- Default jump power
local currentJumpPower = 50  -- Current jump power

-- Create the toggle to enable/disable custom jump power
local JumpPowerToggle = PlayerTab:CreateToggle({
    Name = "Enable Jump Power",
    Callback = function()
        JumpPowerEnabled = not JumpPowerEnabled

        local character = game.Players.LocalPlayer.Character
        if character and character:FindFirstChild("Humanoid") then
            local humanoid = character.Humanoid
            if not JumpPowerEnabled then
                humanoid.JumpPower = defaultJumpPower
                print("Custom Jump Power Disabled")
            else
                print("Custom Jump Power Enabled")
            end
        end
    end
})

-- Create the slider for adjusting jump power
local JumpPowerSlider = PlayerTab:CreateSlider({
    Name = "Jump Power (0-300)",
    Range = {0, 300},
    Increment = 10,
    Suffix = "Unit",
    CurrentValue = currentJumpPower,
    Flag = "Slider1",  -- A flag is the identifier for the configuration file
    Callback = function(Value)
        if JumpPowerEnabled then
            currentJumpPower = Value
            local character = game.Players.LocalPlayer.Character
            if character and character:FindFirstChild("Humanoid") then
                character.Humanoid.JumpPower = currentJumpPower
            end
        end
    end,
})

local PlayerSection = PlayerTab:CreateSection("Spin Bot")

local spinEnabled = false
local spinSpeed = 10 -- Default spin speed

local Player = game.Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")

local RunService = game:GetService("RunService")
local spinConnection

local function updateSpin()
    if spinConnection then
        spinConnection:Disconnect()
    end

    spinConnection = RunService.RenderStepped:Connect(function(deltaTime)
        if spinEnabled and HumanoidRootPart then
            HumanoidRootPart.CFrame = HumanoidRootPart.CFrame * CFrame.Angles(0, math.rad(spinSpeed * deltaTime * 60), 0)
        end
    end)
end

local SpinBotToggle = PlayerTab:CreateToggle({
    Name = "Enable Spin-Bot",
    CurrentValue = false,
    Callback = function(value)
        spinEnabled = value
        if spinEnabled then
            updateSpin()
        else
            if spinConnection then
                spinConnection:Disconnect()
                spinConnection = nil
            end
        end
    end
})

local SpinSpeedSlider = PlayerTab:CreateSlider({
    Name = "Spin Speed (0-1000)",
    Range = {0, 1000},
    Increment = 1,
    Suffix = "Unit",
    CurrentValue = 0,
    Callback = function(value)
        spinSpeed = value
    end
})

local PlayerSection = PlayerTab:CreateSection("Inf Jump")

local InfiniteJumpEnabled = false
local jumpConnection
local jumpPower = 50  -- Default jump power

local InfiniteJumpToggle = PlayerTab:CreateToggle({
    Name = "Infinite-Jump",
    Callback = function()
        InfiniteJumpEnabled = not InfiniteJumpEnabled

        if InfiniteJumpEnabled then
            print("Infinite Jump Enabled")
            jumpConnection = game:GetService("UserInputService").JumpRequest:Connect(function()
                if InfiniteJumpEnabled then
                    local humanoid = game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass('Humanoid')
                    if humanoid then
                        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                        humanoid.JumpHeight = jumpPower  -- Set jump height to slider value
                    end
                end
            end)
        else
            print("Infinite Jump Disabled")
            if jumpConnection then
                jumpConnection:Disconnect()
                jumpConnection = nil
            end
        end
    end
})

local PlayerSection = PlayerTab:CreateSection("No Clip")

local player = game:GetService("Players").LocalPlayer
local noclip = false
local noclipLoop

local function toggleNoclip(state)
    noclip = state
    if noclip then
        noclipLoop = task.spawn(function()
            while noclip do
                if player.Character then
                    for _, part in pairs(player.Character:GetDescendants()) do
                        if part:IsA("BasePart") then
                            part.CanCollide = false
                        end
                    end
                end
                task.wait()
            end
        end)
    else
        if noclipLoop then
            task.cancel(noclipLoop)
        end
        if player.Character then
            for _, part in pairs(player.Character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
    end
end


PlayerTab:CreateToggle({
    Name = "Enable No Clip",
    CurrentValue = false,
    Flag = "NoclipToggle",
    Callback = function(Value)
        toggleNoclip(Value)
    end
})

local UtilitiesTab = Window:CreateTab("Utilities", 4483362458) -- Title, Image
local UtilitiesSection = UtilitiesTab:CreateSection("Screen")

local blackScreen = Instance.new("ScreenGui")
blackScreen.IgnoreGuiInset = true
blackScreen.ZIndexBehavior = Enum.ZIndexBehavior.Global
blackScreen.ResetOnSpawn = false

local blackFrame = Instance.new("Frame")
blackFrame.Size = UDim2.new(1, 0, 1, 0)
blackFrame.Position = UDim2.new(0, 0, 0, 0)
blackFrame.BackgroundColor3 = Color3.new(0, 0, 0)
blackFrame.BackgroundTransparency = 0
blackFrame.Parent = blackScreen

blackScreen.Enabled = false
blackScreen.Parent = game.CoreGui

UtilitiesTab:CreateToggle({
	Name = "Black Screen",
	CurrentValue = false,
	Flag = "BlackScreenToggle",
	Callback = function(Value)
		blackScreen.Enabled = Value
	end,
})

local blackScreen = Instance.new("ScreenGui")
blackScreen.IgnoreGuiInset = true
blackScreen.ZIndexBehavior = Enum.ZIndexBehavior.Global
blackScreen.ResetOnSpawn = false

local blackFrame = Instance.new("Frame")
blackFrame.Size = UDim2.new(1, 0, 1, 0)
blackFrame.Position = UDim2.new(0, 0, 0, 0)
blackFrame.BackgroundColor3 = Color3.new(255,255,255)
blackFrame.BackgroundTransparency = 0
blackFrame.Parent = blackScreen

blackScreen.Enabled = false
blackScreen.Parent = game.CoreGui

UtilitiesTab:CreateToggle({
	Name = "White Screen",
	CurrentValue = false,
	Flag = "BlackScreenToggle",
	Callback = function(Value)
		blackScreen.Enabled = Value
	end,
})

local UtilitiesSection = UtilitiesTab:CreateSection("Time Of Day")

UtilitiesTab:CreateSlider({
    Name = "Time of Day",
    Range = {0, 24},
    Increment = 0.25,
    Suffix = "Hours",
    CurrentValue = 12,
    Flag = "TimeSlider",
    Callback = function(Value)
        local hours = math.floor(Value)
        local minutes = math.floor((Value - hours) * 60)
        game.Lighting.TimeOfDay = string.format("%02d:%02d:00", hours, minutes)
    end,
})

UtilitiesTab:CreateButton({
    Name = "Full Bright",
    Callback = function()
        local lighting = game:GetService("Lighting")
        
        lighting.Brightness = 2
        lighting.ClockTime = 12
        lighting.FogEnd = 100000
        lighting.GlobalShadows = false
        lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
    end,
})

UtilitiesTab:CreateButton({
    Name = "No Fog",
    Callback = function()
        local lighting = game:GetService("Lighting")
        
        lighting.FogStart = 100000
        lighting.FogEnd = 100000
    end,
})

local UtilitiesSection = UtilitiesTab:CreateSection("Player Features")

UtilitiesTab:CreateButton({
    Name = "Naked (Client Sided)",
    Default = false,
    Callback = function(Value)
        local character = game.Players.LocalPlayer.Character
        if character then
            for _, item in pairs(character:GetChildren()) do
                if item:IsA("Shirt") or item:IsA("Pants") or item:IsA("ShirtGraphic") then
                    item:Destroy()
                end
            end

            for _, accessory in pairs(character:GetChildren()) do
                if accessory:IsA("Accessory") then
                    accessory:Destroy()
                end
            end

            if not Value then
            end
        end
    end,
})

UtilitiesTab:CreateButton({
    Name = "No Face",
    Callback = function()
        local character = game.Players.LocalPlayer.Character
        if character then
            local head = character:FindFirstChild("Head")
            if head then
                local face = head:FindFirstChild("face")
                if face then
                    face:Destroy()
                end
            end
        end
    end,
})

local UtilitiesSection = UtilitiesTab:CreateSection("Other")

UtilitiesTab:CreateToggle({
    Name = "Instant Interact",
    Default = false,
    Callback = function(Value)
        local Workspace = game:GetService("Workspace")

        local function updateProximityPrompts()
            for i, v in ipairs(Workspace:GetDescendants()) do
                if v.ClassName == "ProximityPrompt" then
                    v.HoldDuration = Value and 0 or 1  -- Corrected the syntax here
                end
            end
        end

        updateProximityPrompts()

        Workspace.DescendantAdded:Connect(function(descendant)
            if descendant.ClassName == "ProximityPrompt" then
                descendant.HoldDuration = Value and 0 or 1
            end
        end)
    end,
})

UtilitiesTab:CreateButton({
    Name = "Drop All Tools",
    Callback = function()
        local player = game.Players.LocalPlayer
        local backpack = player:FindFirstChild("Backpack")
        local character = player.Character or player.CharacterAdded:Wait()

        -- Drop tools in backpack
        for _, tool in ipairs(backpack:GetChildren()) do
            if tool:IsA("Tool") then
                tool.Parent = character
                task.wait(0.1)
                tool.Parent = workspace
            end
        end

        -- Drop tools already in hand
        for _, tool in ipairs(character:GetChildren()) do
            if tool:IsA("Tool") then
                tool.Parent = workspace
            end
        end
    end,
})

UtilitiesTab:CreateSlider({
    Name = "FOV",
    Range = {0, 120}, 
    Increment = 1,
    Suffix = "Unit",
    CurrentValue = 70,
    Callback = function(Value)
        game.Workspace.CurrentCamera.FieldOfView = Value
    end,
})
