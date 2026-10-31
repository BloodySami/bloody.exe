
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <time.h>
 
typedef struct {
    int health;
    int max_health;
    int money;
    int damage;
    int godmode;
    int kills;
} Player;
 
static void to_lower(char *s) {
    for (; *s; s++) *s = (char)tolower((unsigned char)*s);
}
 
static void show_status(const Player *p) {
    printf("\n=== STATUS ===\n");
    printf("Health : %d/%d%s\n", p->health, p->max_health, p->godmode ? "  [GODMODE]" : "");
    printf("Money  : $%d\n", p->money);
    printf("Damage : %d\n", p->damage);
    printf("Kills  : %d\n", p->kills);
}
 
static void fight(Player *p) {
    int enemy_hp = 30 + rand() % 41;
    printf("\nA zombie appears! (HP: %d)\n", enemy_hp);
 
    while (enemy_hp > 0 && p->health > 0) {
        int dealt = p->damage + rand() % 6;
        enemy_hp -= dealt;
        printf("You hit the zombie for %d damage.\n", dealt);
        if (enemy_hp <= 0) break;
 
        int taken = 5 + rand() % 11;
        if (!p->godmode) p->health -= taken;
        printf("The zombie hits you for %d damage.%s\n", taken,
               p->godmode ? " (blocked by godmode)" : "");
    }
 
    if (p->health <= 0) {
        printf("\nYou died... Game over.\n");
        return;
    }
    int reward = 20 + rand() % 31;
    p->money += reward;
    p->kills++;
    printf("Zombie defeated! You earned $%d.\n", reward);
}
 
static void shop(Player *p) {
    int choice;
    printf("\n=== SHOP ===\n");
    printf("1) Medkit  (+40 HP)  - $50\n");
    printf("2) Weapon upgrade (+5 damage) - $100\n");
    printf("0) Back\n> ");
    if (scanf("%d", &choice) != 1) { while (getchar() != '\n'); return; }
 
    if (choice == 1 && p->money >= 50) {
        p->money -= 50;
        p->health += 40;
        if (p->health > p->max_health) p->health = p->max_health;
        printf("You used a medkit.\n");
    } else if (choice == 2 && p->money >= 100) {
        p->money -= 100;
        p->damage += 5;
        printf("Weapon upgraded!\n");
    } else if (choice == 1 || choice == 2) {
        printf("Not enough money.\n");
    }
}
 
static void cheat_menu(Player *p) {
    char code[64];
    printf("\nEnter cheat code: ");
    if (scanf("%63s", code) != 1) return;
    to_lower(code);
 
    if (strcmp(code, "hesoyam") == 0) {
        p->health = p->max_health;
        p->money += 250000;
        printf("Cheat activated: full health + $250000\n");
    } else if (strcmp(code, "godmode") == 0) {
        p->godmode = !p->godmode;
        printf("Godmode %s\n", p->godmode ? "ON" : "OFF");
    } else if (strcmp(code, "richie") == 0) {
        p->money += 1000000;
        printf("Cheat activated: +$1000000\n");
    } else if (strcmp(code, "bigguns") == 0) {
        p->damage += 50;
        printf("Cheat activated: +50 damage\n");
    } else if (strcmp(code, "bloody") == 0) {
        p->damage += 100;
        p->max_health += 200;
        p->health = p->max_health;
        printf("Cheat activated: BLOODY MODE! (+100 damage, +200 max HP)\n");
    } else {
        printf("Invalid cheat code.\n");
    }
}
 
int main(void) {
    Player p = { 100, 100, 50, 15, 0, 0 };
    int choice;
 
    srand((unsigned)time(NULL));
    printf("=====================\n");
    printf("      B L O O D Y    \n");
    printf("=====================\n");
 
    while (p.health > 0) {
        show_status(&p);
        printf("\n1) Fight\n2) Shop\n3) Rest (+10 HP)\n4) Quit\n5) Enter cheat code\n> ");
        if (scanf("%d", &choice) != 1) {
            while (getchar() != '\n');
            continue;
        }
 
        switch (choice) {
            case 1: fight(&p); break;
            case 2: shop(&p); break;
            case 3:
                p.health += 10;
                if (p.health > p.max_health) p.health = p.max_health;
                printf("You rested.\n");
                break;
            case 4:
                printf("Bye!\n");
                return 0;
            case 5: cheat_menu(&p); break;
            default: printf("Invalid option.\n");
        }
    }
 
    printf("\nFinal kills: %d\n", p.kills);
    printf("Press Enter to exit...");
    while (getchar() != '\n');
    getchar();
    return 0;
