# RPG-ADVENTURE-GAME
LUA
-- ============================================
--        LUA TEXT RPG ADVENTURE GAME
-- ============================================

math.randomseed(os.time())

-- ============================================
-- Player Class
-- ============================================

Player = {}
Player.__index = Player

function Player:new(name, class)
    local obj = {}
    setmetatable(obj, Player)

    obj.name = name
    obj.class = class
    obj.level = 1
    obj.exp = 0
    obj.gold = 50

    if class == "Warrior" then
        obj.hp = 150
        obj.attack = 20
        obj.defense = 10
    elseif class == "Mage" then
        obj.hp = 100
        obj.attack = 30
        obj.defense = 5
    elseif class == "Assassin" then
        obj.hp = 110
        obj.attack = 25
        obj.defense = 8
    end

    return obj
end

function Player:showStats()
    print("\n===== PLAYER STATS =====")
    print("Name:", self.name)
    print("Class:", self.class)
    print("Level:", self.level)
    print("HP:", self.hp)
    print("Attack:", self.attack)
    print("Defense:", self.defense)
    print("EXP:", self.exp)
    print("Gold:", self.gold)
    print("========================\n")
end

function Player:gainExp(amount)
    self.exp = self.exp + amount
    if self.exp >= 100 then
        self.level = self.level + 1
        self.exp = self.exp - 100
        self.hp = self.hp + 20
        self.attack = self.attack + 5
        self.defense = self.defense + 3
        print("\n🎉 LEVEL UP! You are now Level " .. self.level)
    end
end

-- ============================================
-- Enemy Class
-- ============================================

Enemy = {}
Enemy.__index = Enemy

function Enemy:new(name, hp, attack, defense)
    local obj = {}
    setmetatable(obj, Enemy)

    obj.name = name
    obj.hp = hp
    obj.attack = attack
    obj.defense = defense

    return obj
end

function Enemy:show()
    print("\nEnemy:", self.name)
    print("HP:", self.hp)
end

-- ============================================
-- Battle System
-- ============================================

function battle(player, enemy)

    print("\n⚔️  BATTLE STARTED AGAINST " .. enemy.name .. " ⚔️")

    while player.hp > 0 and enemy.hp > 0 do

        print("\n1. Attack")
        print("2. Special Skill (Double Damage)")
        print("3. Heal (20 HP)")
        print("Choose action:")
        local choice = tonumber(io.read())

        if choice == 1 then
            local damage = math.random(player.attack - 5, player.attack + 5)
            damage = damage - enemy.defense
            if damage < 0 then damage = 0 end
            enemy.hp = enemy.hp - damage
            print("You dealt " .. damage .. " damage!")

        elseif choice == 2 then
            local damage = (player.attack * 2) - enemy.defense
            enemy.hp = enemy.hp - damage
            print("🔥 Special Attack! You dealt " .. damage .. " damage!")

        elseif choice == 3 then
            player.hp = player.hp + 20
            print("You healed 20 HP!")
        end

        if enemy.hp <= 0 then
            print("\n🏆 You defeated " .. enemy.name)
            player:gainExp(50)
            player.gold = player.gold + 30
            return
        end

        -- Enemy attack
        local enemyDamage = math.random(enemy.attack - 3, enemy.attack + 3)
        enemyDamage = enemyDamage - player.defense
        if enemyDamage < 0 then enemyDamage = 0 end

        player.hp = player.hp - enemyDamage
        print(enemy.name .. " dealt " .. enemyDamage .. " damage!")

        print("Your HP:", player.hp)
    end

    if player.hp <= 0 then
        print("\n💀 You Died! Game Over.")
        os.exit()
    end
end

-- ============================================
-- Shop System
-- ============================================

function shop(player)
    print("\n🛒 Welcome to the Shop")
    print("1. Increase Attack (Cost: 40 Gold)")
    print("2. Increase Defense (Cost: 40 Gold)")
    print("3. Exit Shop")

    local choice = tonumber(io.read())

    if choice == 1 and player.gold >= 40 then
        player.attack = player.attack + 5
        player.gold = player.gold - 40
        print("Attack Increased!")

    elseif choice == 2 and player.gold >= 40 then
        player.defense = player.defense + 5
        player.gold = player.gold - 40
        print("Defense Increased!")
    end
end

-- ============================================
-- Main Game
-- ============================================

print("======= LUA RPG GAME =======")
print("Enter Your Name:")
local pname = io.read()

print("Choose Class: Warrior / Mage / Assassin")
local pclass = io.read()

local player = Player:new(pname, pclass)

while true do
    print("\n===== MAIN MENU =====")
    print("1. Fight Monster")
    print("2. View Stats")
    print("3. Shop")
    print("4. Fight Boss")
    print("5. Exit Game")

    local choice = tonumber(io.read())

    if choice == 1 then
        local enemy = Enemy:new("Goblin", 80, 15, 5)
        battle(player, enemy)

    elseif choice == 2 then
        player:showStats()

    elseif choice == 3 then
        shop(player)

    elseif choice == 4 then
        local boss = Enemy:new("Dragon King", 250, 30, 15)
        battle(player, boss)

    elseif choice == 5 then
        print("Thanks for playing!")
        break
    end
end
