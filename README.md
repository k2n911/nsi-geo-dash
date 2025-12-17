import pygame
import random

pygame.init()


LARGEUR = 900
HAUTEUR = 500
fenetre = pygame.display.set_mode((LARGEUR, HAUTEUR))
pygame.display.set_caption("Geometry Dash - NSI")

clock = pygame.time.Clock()



BLANC = (255, 255, 255)
BLEU = (0, 120, 255)
ROUGE = (255, 50, 50)
GRIS = (200, 200, 200)
NOIR = (0, 0, 0)


SOL_Y = 400


joueur = pygame.Rect(150, SOL_Y - 40, 40, 40)
gravite = 1
saut = -18
vitesse_y = 0
au_sol = True


obstacles = []
temps_spawn = 0


score = 0
police = pygame.font.SysFont("arial", 30)
police_titre = pygame.font.SysFont("arial", 60)


niveau = 0
vitesse_obstacle = 6
spawn_max = 60


etat = "menu"
running = True

while running:
    clock.tick(60)
    fenetre.fill(BLANC)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if etat == "menu":
                if event.key == pygame.K_1:
                    niveau = 1
                elif event.key == pygame.K_2:
                    niveau = 2
                elif event.key == pygame.K_3:
                    niveau = 3
                if niveau != 0:
                    obstacles.clear()
                    score = 0
                    vitesse_y = 0
                    au_sol = True
                    joueur.y = SOL_Y - 40
                    if niveau == 1:
                        vitesse_obstacle = 6
                        spawn_max = 70
                    elif niveau == 2:
                        vitesse_obstacle = 8
                        spawn_max = 55
                    elif niveau == 3:
                        vitesse_obstacle = 10
                        spawn_max = 40
                    etat = "jeu"
            elif etat == "jeu":
                if event.key == pygame.K_SPACE and au_sol:
                    vitesse_y = saut
                    au_sol = False
            elif etat == "gameover":
                if event.key == pygame.K_SPACE:
                    niveau = 0
                    etat = "menu"
    if etat == "menu":
        titre = police_titre.render("GEOMETRY DASH", True, BLEU)
        fenetre.blit(titre, (200, 120))
        fenetre.blit(police.render("Choisir un niveau :", True, NOIR), (330, 220))
        fenetre.blit(police.render("1 - Facile", True, NOIR), (380, 260))
        fenetre.blit(police.render("2 - Moyen", True, NOIR), (380, 300))
        fenetre.blit(police.render("3 - Difficile", True, NOIR), (380, 340))
    elif etat == "jeu":
        vitesse_y += gravite
        joueur.y += vitesse_y
        if joueur.y >= SOL_Y - 40:
            joueur.y = SOL_Y - 40
            vitesse_y = 0
            au_sol = True
        temps_spawn += 1
        if temps_spawn > spawn_max:
            obstacle = pygame.Rect(LARGEUR, SOL_Y - 40, 30, 40)
            obstacles.append(obstacle)
            temps_spawn = 0
        for obstacle in obstacles:
            obstacle.x -= vitesse_obstacle
            if joueur.colliderect(obstacle):
                etat = "gameover"
        obstacles = [o for o in obstacles if o.x > -40]
        score += 1  
        pygame.draw.rect(fenetre, GRIS, (0, SOL_Y, LARGEUR, 100))
        pygame.draw.rect(fenetre, BLEU, joueur)
        for obstacle in obstacles:
            pygame.draw.rect(fenetre, ROUGE, obstacle)
        fenetre.blit(police.render(f"Niveau : {niveau}", True, NOIR), (20, 20))
        fenetre.blit(police.render(f"Score : {score}", True, NOIR), (20, 60))
    elif etat == "gameover":
        fenetre.blit(police_titre.render("GAME OVER", True, ROUGE), (270, 180))
        fenetre.blit(police.render(f"Score : {score}", True, NOIR), (380, 260))
        fenetre.blit(police.render("Appuie sur ESPACE pour revenir au menu", True, NOIR), (190, 320))
    pygame.display.flip()

pygame.quit()

