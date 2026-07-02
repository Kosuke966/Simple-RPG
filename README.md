import random
import json

print("=== FULL RPG ADVENTURE ===")

# -------------------
# SAVE SYSTEM
# -------------------
def save_game():
    data = {
        "hp": player["hp"],
        "max_hp": player["max_hp"],
        "level": player["level"],
        "xp": player["xp"],
        "gold": player["gold"],
        "potions": player["potions"],
        "weapon": player["weapon"],
        "armor": player["armor"]
    }
    with open("save.json", "w") as f:
        json.dump(data, f)
    print("💾 Game Saved!")

def load_game():
    global player
    try:
        with open("save.json", "r") as f:
            data = json.load(f)
            player.update(data)
        print("📂 Save Loaded!")
    except:
        print("No save found.")

# -------------------
# PLAYER
# -------------------
player = {
    "hp": 120,
    "max_hp": 120,
    "level": 1,
    "xp": 0,
    "gold": 20,
    "potions": 3,
    "weapon": {"name": "Rusty Sword", "min": 8, "max": 15, "crit": 1.5},
    "armor": {"name": "Cloth", "def": 0}
}

# -------------------
# SKILLS
# -------------------
skills = {
    "fireball": {"dmg": 25, "cost": 10},
    "heal": {"heal": 30, "cost": 0}
}

# -------------------
# DAMAGE SYSTEM
# -------------------
def attack():
    w = player["weapon"]
    dmg = random.randint(w["min"], w["max"])

    if random.random() < 0.10:
        print("💥 CRITICAL HIT!")
        dmg = int(dmg * w["crit"])

    return dmg

# -------------------
# LEVEL UP
# -------------------
def level_up():
    player["level"] += 1
    player["xp"] = 0
    player["max_hp"] += 25
    player["hp"] = player["max_hp"]

    print(f"\n🌟 LEVEL UP! You are now level {player['level']}")
    print("❤️ HP increased and restored!")

# -------------------
# SHOP
# -------------------
def shop():
    print("\n🛒 SHOP")
    print("1. Potion (10 gold)")
    print("2. Iron Sword (30 gold)")
    print("3. Steel Armor (40 gold)")
    print("4. Magic Staff (80 gold)")
    print("5. Exit")

    c = input("> ")

    if c == "1" and player["gold"] >= 10:
        player["gold"] -= 10
        player["potions"] += 1
        print("Bought potion!")

    elif c == "2" and player["gold"] >= 30:
        player["gold"] -= 30
        player["weapon"] = {"name": "Iron Sword", "min": 12, "max": 20, "crit": 1.5}
        print("Equipped Iron Sword!")

    elif c == "3" and player["gold"] >= 40:
        player["gold"] -= 40
        player["armor"] = {"name": "Steel Armor", "def": 5}
        print("Equipped Steel Armor!")

    elif c == "4" and player["gold"] >= 80:
        player["gold"] -= 80
        player["weapon"] = {"name": "Magic Staff", "min": 10, "max": 30, "crit": 2}
        print("Equipped Magic Staff!")

# -------------------
# ENEMIES
# -------------------
waves = [
    {"name": "Slime", "hp": 40, "min": 5, "max": 10},
    {"name": "Goblin", "hp": 70, "min": 8, "max": 15},
    {"name": "Orc", "hp": 100, "min": 12, "max": 18},
    {"name": "DRAGON", "hp": 220, "min": 15, "max": 30}
]

# -------------------
# GAME LOOP
# -------------------
for enemy in waves:

    enemy_hp = enemy["hp"]
    print(f"\n👾 {enemy['name']} appears!")

    while enemy_hp > 0 and player["hp"] > 0:

        print("\n--- STATUS ---")
        print(f"HP: {player['hp']}/{player['max_hp']}")
        print(f"Level: {player['level']} XP: {player['xp']}")
        print(f"Gold: {player['gold']} Potions: {player['potions']}")
        print(f"Weapon: {player['weapon']['name']} | Armor: {player['armor']['name']}")
        print(f"Enemy HP: {enemy_hp}")

        print("\n1. Attack")
        print("2. Skill")
        print("3. Heal")
        print("4. Shop")
        print("5. Save")

        choice = input("> ")

        # -------------------
        # PLAYER ACTIONS
        # -------------------
        if choice == "1":
            dmg = attack()
            enemy_hp -= dmg
            print(f"You deal {dmg} damage!")

        elif choice == "2":
            print("1. Fireball (10 mana dmg)")
            print("2. Heal Spell")
            s = input("> ")

            if s == "1":
                dmg = skills["fireball"]["dmg"]
                enemy_hp -= dmg
                print("🔥 Fireball hits!")

            elif s == "2":
                heal = skills["heal"]["heal"]
                player["hp"] = min(player["max_hp"], player["hp"] + heal)
                print("✨ You heal yourself!")

        elif choice == "3":
            if player["potions"] > 0:
                heal = random.randint(20, 40)
                player["hp"] = min(player["max_hp"], player["hp"] + heal)
                player["potions"] -= 1
                print(f"Healed {heal} HP!")
            else:
                print("No potions!")

        elif choice == "4":
            shop()

        elif choice == "5":
            save_game()

        # -------------------
        # ENEMY TURN
        # -------------------
        if enemy_hp > 0:

            if enemy["name"] == "DRAGON" and random.random() < 0.3:
                dmg = random.randint(25, 50)
                print("🔥 DRAGON FIRE BREATH!")
            else:
                dmg = random.randint(enemy["min"], enemy["max"])

            dmg -= player["armor"]["def"]
            dmg = max(0, dmg)

            player["hp"] -= dmg
            print(f"Enemy deals {dmg} damage!")

    # -------------------
    # WIN
    # -------------------
    if player["hp"] > 0:
        print(f"\n✅ {enemy['name']} defeated!")

        reward = random.randint(15, 40)
        player["gold"] += reward
        player["xp"] += 60

        print(f"+{reward} gold +60 XP")

        if player["xp"] >= 100:
            level_up()

    else:
        print("\n💀 You died...")
        break

# -------------------
# END
# -------------------
if player["hp"] > 0:
    print("\n🏆 YOU FINISHED THE GAME!")
    print(f"Final Level: {player['level']}")
