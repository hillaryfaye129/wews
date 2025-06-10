-- My Garden Hub | Grow A Garden Script with ESP, Pet Spawner, Seed Spawner

-- Load a GUI library
local library = loadstring(game:HttpGet("https://pastebin.com/raw/YzYB8t2b"))()
local w = library:CreateWindow('My Garden Hub - Grow A Garden')

-- Auto Farm Section
w:Section('Auto Farm')
w:Toggle('Auto Collect', {flag = "autoCollect"}, function(v)
    getgenv().autoCollect = v
    while getgenv().autoCollect do
        for _,drop in pairs(workspace.Drops:GetChildren()) do
            if drop:IsA("Part") and drop.Name == "Drop" then
                firetouchinterest(game.Players.LocalPlayer.Character.HumanoidRootPart, drop, 0)
                wait()
                firetouchinterest(game.Players.LocalPlayer.Character.HumanoidRootPart, drop, 1)
            end
        end
        wait(0.2)
    end
end)

w:Toggle('Auto Plant', {flag = "autoPlant"}, function(v)
    getgenv().autoPlant = v
    while getgenv().autoPlant do
        game:GetService("ReplicatedStorage").Events.Plant:FireServer()
        wait(1)
    end
end)

w:Toggle('Auto Water', {flag = "autoWater"}, function(v)
    getgenv().autoWater = v
    while getgenv().autoWater do
        game:GetService("ReplicatedStorage").Events.Water:FireServer()
        wait(1)
    end
end)

-- ESP Section
w:Section('ESP')
w:Toggle('ESP Drops', {flag = "espDrops"}, function(state)
    getgenv().espDrops = state

    local function createESP(part)
        if part and not part:FindFirstChild("ESPBox") then
            local box = Instance.new("BoxHandleAdornment")
            box.Name = "ESPBox"
            box.Adornee = part
            box.Size = part.Size + Vector3.new(0.2,0.2,0.2)
            box.Color3 = Color3.fromRGB(0,255,0)
            box.Transparency = 0.7
            box.AlwaysOnTop = true
            box.ZIndex = 10
            box.Parent = part
        end
    end

    local function removeESP(part)
        if part and part:FindFirstChild("ESPBox") then
            part.ESPBox:Destroy()
        end
    end

    spawn(function()
        while getgenv().espDrops do
            for _,drop in pairs(workspace.Drops:GetChildren()) do
                if drop:IsA("Part") and drop.Name == "Drop" then
                    createESP(drop)
                end
            end
            wait(1)
        end
        -- Clean up ESP boxes when toggled off
        for _,drop in pairs(workspace.Drops:GetChildren()) do
            removeESP(drop)
        end
    end)
end)

-- Spawner Section
w:Section('Spawners')
w:Button('Spawn Pet', function()
    -- You may need to adjust the event name and arguments!
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local spawnPetEvent = ReplicatedStorage:FindFirstChild("Events") and ReplicatedStorage.Events:FindFirstChild("SpawnPet")
    if spawnPetEvent then
        spawnPetEvent:FireServer() -- Add arguments if needed, e.g. pet type
    else
        print("SpawnPet event not found. Adjust the event name if needed.")
    end
end)

w:Button('Spawn Seed', function()
    -- You may need to adjust the event name and arguments!
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local spawnSeedEvent = ReplicatedStorage:FindFirstChild("Events") and ReplicatedStorage.Events:FindFirstChild("SpawnSeed")
    if spawnSeedEvent then
        spawnSeedEvent:FireServer() -- Add arguments if needed, e.g. seed type
    else
        print("SpawnSeed event not found. Adjust the event name if needed.")
    end
end)

-- Misc Section
w:Section('Misc')
w:Button('Teleport to Farm', function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(100,5,100)
end)

w:Button('Anti-AFK', function()
    local vu = game:GetService("VirtualUser")
    game:GetService("Players").LocalPlayer.Idled:connect(function()
        vu:Button2Down(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
        wait(1)
        vu:Button2Up(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
    end)
end)
