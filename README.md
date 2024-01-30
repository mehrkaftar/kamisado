import pygame
pygame.init()

screen = pygame.display.set_mode((800, 800))
mode = "menu"
run = True
font = pygame.font.Font('freesansbold.ttf', 32)
selected_piece = "none"
turn = 0  # if turn 0 -> black else if turn 1 -> white
possible_moves = []

board = [['orange', 'blue', 'purple', 'pink', 'yellow', 'red', 'green', 'brown'],
         ['red', 'orange', 'pink', 'green', 'blue', 'yellow', 'brown', 'purple'],
         ['green', 'pink', 'orange', 'red', 'purple', 'brown', 'yellow', 'blue'],
         ['pink', 'purple', 'blue', 'orange', 'brown', 'green', 'red', 'yellow'],
         ['yellow', 'red', 'green', 'brown', 'orange', 'blue', 'purple', 'pink'],
         ['blue', 'yellow', 'brown', 'purple', 'red', 'orange', 'pink', 'green'],
         ['purple', 'brown', 'yellow', 'blue', 'green', 'pink', 'orange', 'red'],
         ['brown', 'green', 'red', 'yellow', 'pink', 'purple', 'blue', 'orange']]
colors = {'blue': (0, 102, 204),
          'red': (204, 0, 0),
          'yellow': (254, 254, 0),
          'green': (0, 203, 0),
          'brown': (102, 52, 0),
          'purple': (76, 0, 153),
          'pink': (255, 51, 153),
          'orange': (255, 128, 0)}
pieces = [{"orange": [0, 0],
           "blue": [0, 1],
           "purple": [0, 2],
           "pink": [0, 3],
           "yellow": [0, 4],
           "red": [0, 5],
           "green": [0, 6],
           "brown": [0, 7]},
          {"orange": [7, 7],
           "blue": [7, 6],
           "purple": [7, 5],
           "pink": [7, 4],
           "yellow": [7, 3],
           "red": [7, 2],
           "green": [7, 1],
           "brown": [7, 0]}]


def menu():
    text = font.render('solo game', True, (0, 0, 0), (255, 0, 0))
    text_rect = text.get_rect()
    text_rect.center = (400, 325)
    screen.fill((0, 255, 255))     # sky blue
    pygame.draw.rect(screen, (255, 0, 0), pygame.Rect(150, 300, 500, 50))     # solo play button
    screen.blit(text, text_rect)

    text = font.render('multiplayer game', True, (0, 0, 0), (0, 204, 0))
    text_rect = text.get_rect()
    text_rect.center = (400, 425)
    pygame.draw.rect(screen, (0, 204, 0), pygame.Rect(150, 400, 500, 50))     # multi play button
    screen.blit(text, text_rect)

    text = font.render('settings', True, (0, 0, 0), (0, 0, 255))
    text_rect = text.get_rect()
    text_rect.center = (400, 525)
    pygame.draw.rect(screen, (0, 0, 255), pygame.Rect(150, 500, 500, 50))     # setting button
    screen.blit(text, text_rect)


# appearance definitions
def main():
    screen.fill((0, 255, 255))
    for i in range(8):
        for x in range(8):
            pygame.draw.rect(screen, colors[board[i][x]], pygame.Rect(x*60+160, i*60+160, 59, 59))

    for x in range(2):
        for i in pieces[x].keys():
            pygame.draw.circle(screen, (x*255, x*255, x*255), (pieces[x][i][1]*60+190, pieces[x][i][0]*60+190), 20)
            pygame.draw.circle(screen, colors[i], (pieces[x][i][1]*60+190, pieces[x][i][0]*60+190), 10)
    if len(possible_moves) >= 1:
        for i in possible_moves:
            pygame.draw.circle(screen, (192, 192, 192), (i[1]*60+190, i[0]*60+190), 5)


def setting():
    pass


# important definitions
def show_moves(location):
    out = []
    a = list(pieces[0].values())
    a.extend(pieces[1].values())
    if turn == 0:
        for i in range(location[0]+1, 8):
            if i < 8 and [i, location[1]] not in a:
                out.append([i, location[1]])
            else:
                break

        for i in range(1, 8):
            if location[0]+i < 8 and location[1]+i < 8 and [location[0]+i, location[1]+i] not in a:
                out.append([location[0]+i, location[1]+i])
            else:
                break

        for i in range(1, 8):
            if location[0]+i < 8 and location[1]-i > -1 and [location[0]+i, location[1]-i] not in a:
                out.append([location[0]+i, location[1]-i])
            else:
                break
        return out
    else:
        for i in range(location[0]-1, -1, -1):
            if i > -1 and [i, location[1]] not in a:
                out.append([i,location[1]])
            else:
                break

        for i in range(1, 8):
            if location[0]-i > -1 and location[1]-i > -1 and [location[0]-i, location[1]-i] not in a:
                out.append([location[0]-i, location[1]-i])
            else:
                break

        for i in range(1, 8):
            if location[0]-i > -1 and location[1]+i < 8 and [location[0]-i, location[1]+i] not in a:
                out.append([location[0]-i, location[1]+i])
            else:
                break
        return out


pos = [0, 0]
while run:
    pygame.display.update()
    for event in pygame.event.get():
        if event.type == pygame.MOUSEBUTTONDOWN:
            pos = list(pygame.mouse.get_pos())

            if mode == "menu":
                menu()
                if 150 <= pos[0] <= 650 and 300 <= pos[1] <= 550:
                    if 300 <= pos[1] <= 350:
                        mode = "solo game"
                        main()
                    elif 400 <= pos[1] <= 450:
                        mode = "multiplayer game"
                        main()
                    elif 500 <= pos[1] <= 550:
                        mode = "setting"
                        setting()

            elif mode == "setting":
                setting()

            elif mode == "solo game":
                main()

            elif mode == "multiplayer game":
                pos[0], pos[1] = (pos[1]-160)//60, (pos[0]-160)//60

                if pos in pieces[turn].values() and selected_piece == "none":
                    selected_piece = list(pieces[turn].keys())[list(pieces[turn].values()).index(pos)]
                    possible_moves = show_moves(pos)

                elif pos in possible_moves:
                    if pos[0] == 7 - turn * 7:
                        mode = "black won" if turn == 0 else "white won"
                    else:
                        pieces[turn][selected_piece] = pos
                        selected_piece = board[pos[0]][pos[1]]
                        turn = 0 if turn == 1 else 1
                        possible_moves = show_moves(pieces[turn][selected_piece])
                        if len(possible_moves) == 0:
                            selected_piece = "none"
                            turn = 0 if turn == 1 else 1

                main()

            elif mode == "black won":
                screen.fill((0, 255, 255))
                text = font.render('black won', True, (0, 0, 0), (255, 0, 0))
                text_rect = text.get_rect()
                text_rect.center = (400, 325)
                screen.blit(text, text_rect)

            elif mode == "white won":
                screen.fill((0, 255, 255))
                text = font.render('white won', True, (0, 0, 0), (255, 0, 0))
                text_rect = text.get_rect()
                text_rect.center = (400, 325)
                screen.blit(text, text_rect)# kamisado
