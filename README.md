import random
import json

print("===================================")
print("      ⚔️ SHADOW KINGDOM RPG ⚔️     ")
print("===================================\n")

# -------------------
# SAVE SYSTEM
# -------------------
def save_game():
    data = player
    with open("save.json", "w") as f:
        json.dump(data, f)
    print("💾 Game saved.")

def load_game():
    global player
    try:
        with open("save.json", "r") as f:
            player = json.load(f)
        print("📂 Save loaded.")
    except:
        print("No save file found.")

# -------------------
# PLAYER
# -------------------
player = {
    "hp": 120,
    "max_hp": 120,
    "level": 1,
    "xp": 0,
    "gold": 20,
    "potions": 2,
    "weapon": "Rusty Sword",
    "alive": True
}

# -------------------
# CORE SYSTEMS
# -------------------
def damage(min_dmg, max_dmg):
    return random.randint(min_dmg, max_dmg)

def level_up():
    player["level"] += 1
    player["max_hp"] += 25
    player["hp"] = player["max_hp"]
    player["xp"] = 0
    print("\n🌟 LEVEL UP!")
    print(f"You are now level {player['level']}.")

def fight(enemy_name, enemy_hp, min_dmg, max_dmg):
    print(f"\n👾 {enemy_name} appears!")

    while enemy_hp > 0 and player["hp"] > 0:

        print("\n--- STATUS ---")
        print(f"HP: {player['hp']}/{player['max_hp']} | Level: {player['level']}")
        print(f"Enemy HP: {enemy_hp}")
        print(f"Weapon: {player['weapon']} | Potions: {player['potions']}")

        print("\n1. Attack")
        print("2. Heal")
        print("3. Run")

        c = input("> ")

        if c == "1":
            dmg = damage(10, 25)
            if random.random() < 0.10:
                print("💥 CRITICAL HIT!")
                dmg *= 2
            enemy_hp -= dmg
            print(f"You deal {dmg} damage!")

        elif c == "2":
            if player["potions"] > 0:
                heal = random.randint(20, 40)
                player["hp"] = min(player["max_hp"], player["hp"] + heal)
                player["potions"] -= 1
                print(f"✨ You heal {heal} HP!")
            else:
                print("No potions!")

        elif c == "3":
            if random.random() < 0.5:
                print("🏃 You escaped!")
                return False
            else:
                print("❌ Failed to escape!")

        # enemy attack
        if enemy_hp > 0:
            dmg = damage(min_dmg, max_dmg)
            player["hp"] -= dmg
            print(f"Enemy hits you for {dmg} damage!")

        if player["hp"] <= 0:
            player["alive"] = False
            print("\n💀 You died...")
            return False

    print(f"\n✅ {enemy_name} defeated!")
    player["xp"] += 50
    player["gold"] += random.randint(10, 30)

    if player["xp"] >= 100:
        level_up()

    return True

# -------------------
# STORY SYSTEM
# -------------------
def intro():
    print("📜 STORY:")
    print("The Shadow Kingdom has fallen into darkness.")
    print("Monsters roam freely, and the King has vanished.")
    print("You are a wandering warrior chosen to restore balance...\n")
    input("Press Enter to begin your journey...")

def village():
    print("\n🏡 You arrive at a small village.")
    print("Villagers beg you to help them.")
    print("A goblin is attacking nearby farms.")

    choice = input("Help? (yes/no): ")

    if choice == "yes":
        fight("Goblin Raider", 60, 8, 15)
    else:
        print("You ignored the villagers...")

def forest():
    print("\n🌲 You enter the cursed forest.")
    print("Strange whispers surround you.")

    fight("Forest Wolf", 80, 10, 18)

def cave():
    print("\n🕳️ You enter a dark cave.")
    print("Something massive is breathing inside...")

    fight("Cave Troll", 120, 12, 22)

def boss_castle():
    print("\n🏰 You reach the Dark Castle.")
    print("The final enemy awaits...")

    print("\n🔥 FINAL BOSS: THE SHADOW KING 🔥")

    fight("Shadow King", 200, 15, 30)

    if player["alive"]:
        print("\n🏆 YOU DEFEATED THE SHADOW KING!")
        print("🌅 Light returns to the kingdom.")
        print("YOU ARE A LEGEND.")

# -------------------
# GAME FLOW
# -------------------
intro()

village()

if player["alive"]:
    forest()

if player["alive"]:
    cave()

if player["alive"]:
    boss_castle()

print("\n=== GAME OVER ===")
print(f"Final Level: {player['level']}")
print(f"Gold: {player['gold']}")
