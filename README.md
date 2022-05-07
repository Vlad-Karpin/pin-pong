import sys, random

def ball_animation():
	global ball_speed_x, ball_speed_y, player_score, opponent_score, score_time
	
	ball.x += ball_speed_x
	ball.y += ball_speed_y

	if ball.top <= 0 or ball.bottom >= screen_height:
		ball_speed_y *= -1
		
# игрок
	if ball.left <= 0: 
		score_time = time.get_ticks()
		player_score += 1
		
	# очки игрока и опонента
	if ball.right >= screen_width:
		score_time = time.get_ticks()
		opponent_score += 1
		
	if ball.colliderect(player) and ball_speed_x > 0:
		if abs(ball.right - player.left) < 10:
			ball_speed_x *= -1	
		elif abs(ball.bottom - player.top) < 10 and ball_speed_y > 0:
			ball_speed_y *= -1
		elif abs(ball.top - player.bottom) < 10 and ball_speed_y < 0:
			ball_speed_y *= -1

	if ball.colliderect(opponent) and ball_speed_x < 0:
		if abs(ball.left - opponent.right) < 10:
			ball_speed_x *= -1	
		elif abs(ball.bottom - opponent.top) < 10 and ball_speed_y > 0:
			ball_speed_y *= -1
		elif abs(ball.top - opponent.bottom) < 10 and ball_speed_y < 0:
			ball_speed_y *= -1
		

def player_animation():
	player.y += player_speed

	if player.top <= 0:
		player.top = 0
	if player.bottom >= screen_height:
		player.bottom = screen_height

def opponent_ai():
	if opponent.top < ball.y:
		opponent.y += opponent_speed
	if opponent.bottom > ball.y:
		opponent.y -= opponent_speed

	if opponent.top <= 0:
		opponent.top = 0
	if opponent.bottom >= screen_height:
		opponent.bottom = screen_height

def ball_start():
	global ball_speed_x, ball_speed_y, ball_moving, score_time

	ball.center = (screen_width/2, screen_height/2)
	current_time = time.get_ticks()

	if current_time - score_time < 700:
		number_three = basic_font.render("3",False,light_grey)
		screen.blit(number_three,(screen_width/2 - 10, screen_height/2 + 20))
	if 700 < current_time - score_time < 1400:
		number_two = basic_font.render("2",False,light_grey)
		screen.blit(number_two,(screen_width/2 - 10, screen_height/2 + 20))
	if 1400 < current_time - score_time < 2100:
		number_one = basic_font.render("1",False,light_grey)
		screen.blit(number_one,(screen_width/2 - 10, screen_height/2 + 20))

	if current_time - score_time < 2100:
		ball_speed_y, ball_speed_x = 0,0
	else:
		ball_speed_x = 7 * random.choice((1,-1))
		ball_speed_y = 7 * random.choice((1,-1))
		score_time = None

# обновление кадров
mixer.pre_init(44100,-16,1, 1024)
init()
clock = time.Clock()

# создание окна
screen_width = 1280
screen_height = 960
screen = display.set_mode((screen_width,screen_height))
display.set_caption('пин понг')

# цвет
light_grey = (200,200,200)
bg_color = Color('grey12')

# игровые спрайты
ball = Rect(screen_width / 2 - 15, screen_height / 2 - 15, 30, 30)
player = Rect(screen_width - 20 - 15, screen_height / 2 - 15, 10 +300,140)
opponent = Rect(10, screen_height / 2 - 70, 10,140)

# рандомайзер
ball_speed_x = 7 * random.choice((1,-1))
ball_speed_y = 7 * random.choice((1,-1))
player_speed = 0
opponent_speed = 7
ball_moving = False
score_time = True

# текст начала
player_score = 0
opponent_score = 0
basic_font = font.Font('freesansbold.ttf', 32)


while True:
	for event in event.get():
		if event.type == QUIT:
			quit()
			sys.exit()
		if event.type == KEYDOWN:
			if event.key == K_UP:
				player_speed -= 6
			if event.key == K_DOWN:
				player_speed += 6
		if event.type == KEYUP:
			if event.key == K_UP:
				player_speed += 6
			if event.key == K_DOWN:
				player_speed -= 6
	
	#анимация
	ball_animation()
	player_animation()
	opponent_ai()

	# визуально чтоб увидеть обект 
	screen.fill(bg_color)
	draw.rect(screen, light_grey, player)
	draw.rect(screen, light_grey, opponent)
	draw.ellipse(screen, light_grey, ball)
	draw.aaline(screen, light_grey, (screen_width / 2, 0),(screen_width / 2, screen_height))

	if score_time:
		ball_start()

	player_text = basic_font.render(f'{player_score}',False,light_grey)
	screen.blit(player_text,(660,470))

	opponent_text = basic_font.render(f'{opponent_score}',False,light_grey)
	screen.blit(opponent_text,(600,470))

	display.flip()
	clock.tick(60)
