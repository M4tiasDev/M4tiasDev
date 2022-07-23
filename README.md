local dupping = false
local walkSpeed = 16
local plrs = game:GetService'Players'
local plr = plrs.LocalPlayer
local mouse = plr:GetMouse()
local rep = game:GetService'ReplicatedStorage'
local uis = game:GetService'UserInputService'
local ts = game:GetService'TweenService'
local rs = game:GetService'RunService'

spawn(function()
    while true do rs.RenderStepped:wait()
        pcall(function()
            if not uis:IsKeyDown(Enum.KeyCode.LeftShift) then
                plr.Character.Humanoid.WalkSpeed = walkSpeed
            elseif uis:IsKeyDown(Enum.KeyCode.LeftShift) then
                plr.Character.Humanoid.WalkSpeed = walkSpeed+4
            end
        end)
    end
end)

local function Notify(title,text,duration)
    game:GetService'StarterGui':SetCore('SendNotification',{
        Title = tostring(title),
        Text = tostring(text),
        Duration = tonumber(duration),
    })
end
local function AddCd(tool,Cd)
    local cd = Instance.new('IntValue',tool)
    cd.Name = 'ClientCD'
    game.Debris:AddItem(cd,Cd)
end
local function Shoot(firstPos,nextPos,Revolver)
    if Revolver:FindFirstChild'Barrel' and Revolver.Barrel:FindFirstChild'Attachment' then
        if Revolver.Barrel.Attachment:FindFirstChild'Sound' then
            local SoundClone = Revolver.Barrel.Attachment.Sound:Clone()
            SoundClone.Name = 'Uncopy'
            SoundClone.Parent = Revolver.Barrel.Attachment
            SoundClone:Play()
            game.Debris:AddItem(SoundClone, 1)
        end
        local FilterTable = {}
        table.insert(FilterTable, plr.Character)
        table.insert(FilterTable, game.Workspace['Target Filter'])
        for _, v in pairs(game.Workspace:GetDescendants()) do
            if v.ClassName == 'Accessory' then
                table.insert(FilterTable, v)
            end
        end
        local a_1, a_2, a_3 = game.Workspace:FindPartOnRayWithIgnoreList(Ray.new(firstPos, (nextPos - firstPos).Unit * 100), FilterTable)
        local BulletCl = rep['Revolver Bullet']:Clone()
        game.Debris:AddItem(BulletCl, 1)
        BulletCl.Parent = game.Workspace['Target Filter']
        local mg = (firstPos - a_2).Magnitude
        BulletCl.Size = Vector3.new(0.2, 0.2, mg)
        BulletCl.CFrame = CFrame.new(firstPos, nextPos) * CFrame.new(0, 0, -mg / 2)
        ts:Create(BulletCl, TweenInfo.new(0.3, Enum.EasingStyle.Linear, Enum.EasingDirection.Out), {
            Size = Vector3.new(0.05, 0.05, mg), 
            Transparency = 1
        }):Play()
        if a_1 then
            local expPart = Instance.new'Part'
            game.Debris:AddItem(expPart, 2)
            expPart.Name = 'Exploding Neon Part'
            expPart.Anchored = true
            expPart.CanCollide = false
            expPart.Shape = 'Ball'
            expPart.Material = Enum.Material.Neon
            expPart.BrickColor = BulletCl.BrickColor
            expPart.Size = Vector3.new(0.1, 0.1, 0.1)
            expPart.Parent = game.Workspace['Target Filter']
            expPart.Position = a_2
            ts:Create(expPart, TweenInfo.new(0.2, Enum.EasingStyle.Linear, Enum.EasingDirection.Out), {
                Size = Vector3.new(2, 2, 2), 
                Transparency = 1
            }):Play()
            if Revolver:FindFirstChild'DamageRemote' and a_1.Parent:FindFirstChild'Humanoid' then
                Revolver.DamageRemote:FireServer(a_1.Parent.Humanoid)
            end
        end
    end
end

local Lib = loadstring(game:HttpGet("https://pastebin.com/raw/hfW9AtPz",true))()
local Table = {}
local x = Lib:CreateWindow("Gengar Z")
x:Section("Controls")

x:Button("Drop GR (X)",function()
        wait()
        for _,v in next,plr.Backpack:GetChildren() do
            if v.Name == 'Grenade' and v:FindFirstChild'RemoteEvent' then
                v.Parent = plr.Character
                v.RemoteEvent:FireServer(mouse.Hit.LookVector)
                wait()
                v.Parent = plr.Backpack
            end
         end
     end)

x:Button("Drop C4 (B)",function()
        for _,v in next,plr.Backpack:GetChildren() do
            if v.Name == 'C4' and v:FindFirstChild'RemoteEvent' then
                v.Parent = plr.Character
                v.RemoteEvent:FireServer(mouse.Hit.LookVector)
                wait()
                v.Parent = plr.Backpack
            end
        end
    end)

x:Button("Drop ST (N)",function()
        for _,v in next,plr.Backpack:GetChildren() do
            if v.Name == 'Spiked Trap' then
                v.Parent = plr.Character
                v:Activate()
                wait()
                v.Parent = plr.Backpack
            end
        end
    end)

x:Button("Spam Bullet (V)",function()
        for _,v in next,plr.Backpack:GetChildren() do
            if plr.Character and plr.Character:FindFirstChild'Head' and v.Name == 'Kawaii Revolver' and v:FindFirstChild'ReplicateRemote' and not v:FindFirstChild'ClientCD' and v:FindFirstChild'Barrel' and v.Barrel:FindFirstChild'Attachment' then
                AddCd(v,2)
                v.Parent = plr.Character
                Shoot(v.Barrel.Attachment.WorldPosition,mouse.Hit.p,v)
                v.ReplicateRemote:FireServer(mouse.Hit.p)
                rs.Stepped:wait()
                v.Parent = plr.Backpack
            end
        end
    end)

x:Button("DShoot (C)",function()
        local guns = 0
        for _,v in next,plr.Backpack:GetChildren() do
            if guns<= 10 and plr.Character and plr.Character:FindFirstChild'Head' and v.Name == 'Kawaii Revolver' and not v:FindFirstChild'ClientCD' and v:FindFirstChild'ReplicateRemote' and v:FindFirstChild'Barrel' and v.Barrel:FindFirstChild'Attachment' then
                guns = guns+1
                AddCd(v,2)
                v.Parent = plr.Character
                Shoot(plr.Character.Head.Position,mouse.Hit.p,v)
                v.ReplicateRemote:FireServer(mouse.Hit.p)
                v.Parent = plr.Backpack
            end
        end
    end)

plr:GetMouse().KeyDown:Connect(function(key)
    if key == 'r' then
        tar = nil
        for _,v in next,workspace:GetDescendants() do
            if v.Name == 'SelectedPlayer' then
                v:Destroy()
            end
        end
        local n_plr, dist
        for _, v in pairs(game.Players:GetPlayers()) do
            if v ~= plr and plr.Character and plr.Character:FindFirstChild'HumanoidRootPart' then
                local distance = v:DistanceFromCharacter(plr.Character.HumanoidRootPart.Position)
                if v.Character and (not dist or distance <= dist) and v.Character:FindFirstChildOfClass'Humanoid' and v.Character:FindFirstChildOfClass'Humanoid'.Health>0 and v.Character:FindFirstChild'HumanoidRootPart' then
                    dist = distance
                    n_plr = v
                end
            end
        end
        local sp = Instance.new('SelectionBox',n_plr.Character.HumanoidRootPart)
        sp.Name = 'SelectedPlayer'
        sp.Adornee = n_plr.Character.HumanoidRootPart
        tar = n_plr
    elseif key == 'q' and tar and plr.Character then
        for _,v in next,plr.Character:GetDescendants() do
            if v:IsA'Tool' and v.Name ~= 'Kawaii Revolver' and not v:FindFirstChild'ClientCD' and v:FindFirstChild'DamageRemote' and v:FindFirstChild'Cooldown' and tar and tar.Character and tar.Character:FindFirstChildOfClass'Humanoid' then
                AddCd(v,v.Cooldown.Value)
                v.DamageRemote:FireServer(tar.Character:FindFirstChildOfClass'Humanoid')
                if v:FindFirstChild'Attack' and plr.Character:FindFirstChildOfClass'Humanoid' then
                    plr.Character:FindFirstChildOfClass'Humanoid':LoadAnimation(v.Attack):Play()
                end
                if v:FindFirstChild'Blade' then
                    for _,x in next,v.Blade:GetChildren() do
                        if x:IsA'Sound' then
                            x:Play()
                        end
                    end
                end
            elseif v:IsA'Tool' and v.Name == 'Kawaii Revolver' and not v:FindFirstChild'ClientCD' and v:FindFirstChild'ReplicateRemote' and v:FindFirstChild'Barrel' and v.Barrel:FindFirstChild'Attachment' and tar and tar.Character and tar.Character:FindFirstChild'HumanoidRootPart' then
                v.Parent = plr.Character
                AddCd(v,2)
                rs.Stepped:wait()
                Shoot(v.Barrel.Attachment.WorldPosition,tar.Character.HumanoidRootPart.Position,v)
                v.ReplicateRemote:FireServer(tar.Character.HumanoidRootPart.Position)
            end
        end
    elseif key == 'p' then
        local guns = .0
		for i,v in pairs(game.Players.LocalPlayer.Backpack:GetChildren()) do
			if v:IsA'Tool' and v.Name == 'Kawaii Revolver' and not v:FindFirstChild'ClientCD' and v:FindFirstChild'ReplicateRemote' and v:FindFirstChild'Barrel' and v.Barrel:FindFirstChild'Attachment' and tar and tar.Character and tar.Character:FindFirstChild'HumanoidRootPart' then
                v.Parent = plr.Character
                AddCd(v,2)
				game.Players.LocalPlayer.Character.Humanoid:EquipTool(v)
				v:Activate()
				for i=1,0 do
					game:GetService("RunService").Heartbeat:Wait()
				end
			end
        end
    elseif key == 'v' then
        for _,v in next,plr.Backpack:GetChildren() do
            if plr.Character and plr.Character:FindFirstChild'Head' and v.Name == 'Kawaii Revolver' and v:FindFirstChild'ReplicateRemote' and not v:FindFirstChild'ClientCD' and v:FindFirstChild'Barrel' and v.Barrel:FindFirstChild'Attachment' then
                AddCd(v,2)
                v.Parent = plr.Character
                Shoot(v.Barrel.Attachment.WorldPosition,mouse.Hit.p,v)
                v.ReplicateRemote:FireServer(mouse.Hit.p)
                rs.Stepped:wait()
                v.Parent = plr.Backpack
            end
        end
    elseif key == 'c' and plr:FindFirstChild'Backpack' then
        local guns = 0
        for _,v in next,plr.Backpack:GetChildren() do
            if guns<= 10 and plr.Character and plr.Character:FindFirstChild'Head' and v.Name == 'Kawaii Revolver' and not v:FindFirstChild'ClientCD' and v:FindFirstChild'ReplicateRemote' and v:FindFirstChild'Barrel' and v.Barrel:FindFirstChild'Attachment' then
                guns = guns+1
                AddCd(v,2)
                v.Parent = plr.Character
                Shoot(plr.Character.Head.Position,mouse.Hit.p,v)
                v.ReplicateRemote:FireServer(mouse.Hit.p)
                v.Parent = plr.Backpack
            end
        end
    elseif key == 'n' then
        for _,v in next,plr.Backpack:GetChildren() do
            if v.Name == 'Spiked Trap' then
                v.Parent = plr.Character
                v:Activate()
                wait()
                v.Parent = plr.Backpack
            end
        end
        elseif key == 'z' then
		for i,v in pairs(game.Players.LocalPlayer.Backpack:GetChildren()) do
			if v.Name == "Blood Dagger" then
				game.Players.LocalPlayer.Character.Humanoid:EquipTool(v)
                for i=1,2 do
					game:GetService("RunService").Heartbeat:Wait()
				end
			end
		end
        elseif key == 'k' then
		for i,v in pairs(game.Players.LocalPlayer.Backpack:GetChildren()) do
			if v.Name == "Blood Dagger" then
				game.Players.LocalPlayer.Character.Humanoid:EquipTool(v)
                v:Activate()
                wait(.1)
                for i=1,2 do
					game:GetService("RunService").Heartbeat:Wait()
				end
			end
		end
        elseif key == 'x' then
         wait(1)
        for _,v in next,plr.Backpack:GetChildren() do
            if v.Name == 'Grenade' then
                pcall(function()
                    v.Parent = plr.Character
                    wait()
                    v.RemoteEvent:FireServer(mouse.Hit.LookVector)
                end)
            end
        end
        elseif key == 'b' then
         wait(.1)
        for _,v in next,plr.Backpack:GetChildren() do
            if v.Name == 'C4' then
                pcall(function()
                    v.Parent = plr.Character
                    wait()
                    v.RemoteEvent:FireServer(mouse.Hit.LookVector)
                end)
            end
        end
    elseif key == 'l' and plr.Character then
        if not dupping then
            Notify('Dupping','Time left: 5 sec',0)
            spawn(function()
                dupping = true
                local c = 1
                for i = 1,80 do
                    pcall(function()
                        if c>#rep.Weapons:GetChildren() then
                            c = 1
                        end
                    end)
                    for _,v in next,plr.Character:GetChildren() do
                        if v.Name == 'Loaded' and v:IsA'IntValue' then
                            v:Destroy()
                        end
                    end
                    for _,v in next,plr.PlayerGui:GetDescendants() do
                        if v:IsA'RemoteEvent' and v.Name == 'RemoteEvent' then
                            pcall(function()
                                v:FireServer(rep.Weapons:GetChildren()[c].Name)
                                c=c+1
                            end)
                        end
                    end
                    wait(0)
                end
                Notify('Dupe','Done!',0)
                dupping = false
            end)
        else Notify('Dupping','Already dupping',0)
        end
    end
end)

x:Button("STools (L)",function()
end)

x:Button("ASTool (Z)",function()
end)

x:Button("AHit (K)",function()
end)

x:Button("SPlayers (P)",function()
end)

x:Toggle("TAll",{location = Table, flag = "Toggle"},function()
		for i,v in pairs(game.Players.LocalPlayer.Backpack:GetChildren()) do
			if v:FindFirstChild("DamageRemote") then
				game.Players.LocalPlayer.Character.Humanoid:EquipTool(v)

				local targ
				local range = 10000
				for i,v in pairs(workspace:GetChildren()) do
					if v~= game.Players.LocalPlayer.Character and not table.find(_G.IgnoreList,v.Name) then
						local vhum = v:FindFirstChild("Humanoid")
						local vroot = v:FindFirstChild("HumanoidRootPart")
						if vhum and vroot then
							local dist = (vroot.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).magnitude
							if vhum.Health>0 and not v:FindFirstChild("ForceField") and dist <= range then
								targ = vhum
								range = dist
							end
						end
					end
				end
				game:GetService("RunService").Heartbeat:Wait()
				if targ then
					v.DamageRemote:FireServer(targ)
				end
			end
		end
   end)

x:Section("Mierda Extra")

x:Button("Inf Dash",function()
	originalfunction = hookfunction(wait, function(seconds)
		if seconds == 2 then return end
		return originalfunction(seconds)
	end)
end)

x:Button("Nuke",function()
        for _,v in next,plr.Backpack:GetChildren() do
            if v.Name == 'Grenade' and v:FindFirstChild'RemoteEvent' then
                v.Parent = plr.Character
                v.RemoteEvent:FireServer(mouse.Hit.LookVector)
                v.Parent = plr.Backpack
            end
        end
    end)

x:Button("Crash",function()
        for _,v in next,plr.Backpack:GetChildren() do
            if v.Name == 'C4' and v:FindFirstChild'RemoteEvent' then
                v.Parent = plr.Character
                v.RemoteEvent:FireServer(mouse.Hit.LookVector)
                v.Parent = plr.Backpack
            end
        end
    end)

x:Button("GR (nil)",function()
        for i,v in pairs(game.Players.LocalPlayer.Backpack:GetChildren()) do
            if v.Name == "Grenade" then
                game.Players.LocalPlayer.Character.Humanoid:EquipTool(v)
                Workspace.HelloJesus009.Grenade.RemoteEvent:FireServer()
            game:GetService("RunService").Heartbeat:Wait()
            end
        end
    end)

x:Button("C4 (nil)",function()
        for i,v in pairs(game.Players.LocalPlayer.Backpack:GetChildren()) do
            if v.Name == "C4" then
                game.Players.LocalPlayer.Character.Humanoid:EquipTool(v)
                Workspace.HelloJesus009.C4.RemoteEvent:FireServer()
            game:GetService("RunService").Heartbeat:Wait()
            end
        end
    end)

x:Button("GPass",function()
local Old;
Old = hookmetamethod(game, "__namecall", function(...)
   if not checkcaller() and getnamecallmethod() == "UserOwnsGamePassAsync" then
       return true
   end
   return Old(...)
end)
end)

x:Button("Centre",function()
	game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-103, 66, 104)
end)

x:Button("Godmode",function()
        Notify('Godmode','Loaded!',3)
        for _,v in next,plr.PlayerGui:GetChildren() do
            if v:IsA'ScreenGui' and v.Name ~= 'Chat' and v.Name ~= 'BubbleChat' then
                v.ResetOnSpawn = false
                spawn(function()
                    wait(5)
                    plr.CharacterAdded:wait()
                    if v then
                        v:Destroy()
                    end
                end)
            elseif v:IsA'LocalScript' then
                v.Parent = plr
                spawn(function()
                    wait(5)
                    v.Parent = plr.PlayerGui
                end)
            end
        end
        if plr.Character and plr.Character:FindFirstChildOfClass'Humanoid' then
            if plr.Character:FindFirstChild'Ragdolled' and plr.Character.Ragdolled:FindFirstChildOfClass'Script' then
                plr.Character.Ragdolled:FindFirstChildOfClass'Script':Destroy()
            end
            local char = plr.Character
            char.Archivable = true
            local new = char:Clone()
            new.Parent = workspace
            plr.Character = new
            wait(2)
            local oldhum = char:FindFirstChildOfClass'Humanoid'
            local newhum = oldhum:Clone()
            newhum.Parent = char
            newhum.RequiresNeck = false
            oldhum.Parent = nil
            wait(2)
            plr.Character = char
            new:Destroy()
            wait(1)
            newhum:GetPropertyChangedSignal('Health'):Connect(
                function()
                    if newhum.Health <= 0 then
                        oldhum.Parent = plr.Character
                        wait(1)
                        oldhum:Destroy()
                    end
                end)
            workspace.CurrentCamera.CameraSubject = char
        end
        Notify('Godmode','Click 2 times to remove.',3)
    end)

x:Section("Weapons")

x:Button("Mace",function()
	if game.Players.LocalPlayer.Character:FindFirstChild("Loaded") then
		game.Players.LocalPlayer.Character.Loaded:Destroy()
	end
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").RemoteEvent:FireServer("Mace")
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").Enabled = false
end)

x:Button("Chainsaw",function()
	if game.Players.LocalPlayer.Character:FindFirstChild("Loaded") then
		game.Players.LocalPlayer.Character.Loaded:Destroy()
	end
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").RemoteEvent:FireServer("Chainsaw")
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").Enabled = false
end)

x:Button("Knife",function()
	if game.Players.LocalPlayer.Character:FindFirstChild("Loaded") then
		game.Players.LocalPlayer.Character.Loaded:Destroy()
	end
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").RemoteEvent:FireServer("Knife")
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").Enabled = false
end)

x:Button("Pan",function()
	if game.Players.LocalPlayer.Character:FindFirstChild("Loaded") then
		game.Players.LocalPlayer.Character.Loaded:Destroy()
	end
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").RemoteEvent:FireServer("Pan")
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").Enabled = false
end)

x:Button("Pencil",function()
	if game.Players.LocalPlayer.Character:FindFirstChild("Loaded") then
		game.Players.LocalPlayer.Character.Loaded:Destroy()
	end
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").RemoteEvent:FireServer("Pencil")
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").Enabled = false
end)

x:Button("Baseball Bat",function()
	if game.Players.LocalPlayer.Character:FindFirstChild("Loaded") then
		game.Players.LocalPlayer.Character.Loaded:Destroy()
	end
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").RemoteEvent:FireServer("Baseball Bat")
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").Enabled = false
end)

x:Button("Scythe",function()
	if game.Players.LocalPlayer.Character:FindFirstChild("Loaded") then
		game.Players.LocalPlayer.Character.Loaded:Destroy()
	end
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").RemoteEvent:FireServer("Scythe")
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").Enabled = false
end)

x:Button("Emerald Greatsword",function()
	if game.Players.LocalPlayer.Character:FindFirstChild("Loaded") then
		game.Players.LocalPlayer.Character.Loaded:Destroy()
	end
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").RemoteEvent:FireServer("Emerald Greatsword")
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").Enabled = false
end)

x:Button("Blood Dagger",function()
	if game.Players.LocalPlayer.Character:FindFirstChild("Loaded") then
		game.Players.LocalPlayer.Character.Loaded:Destroy()
	end
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").RemoteEvent:FireServer("Blood Dagger")
	game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Menu Screen").Enabled = false
end)

x:Section("Credits")

x:Button("Made By El Matias",function()
end)

x:Button("MatiasFortnite2007#7946",function()
end)

x:Button("Scripts By KAKOYTO",function()
end)

x:Button("HideBind (F6)",function()
end)
