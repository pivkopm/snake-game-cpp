# snake-game-cpp
Snake game
void Input(Game& game)
{
 if (_kbhit())
 { // Если нажата клавиша
  switch (_getch())
  { // Получаем нажатую клавишу
  case 'a': if (game.dir != Game::RIGHT) game.dir = Game::LEFT; break;
  case 'd': if (game.dir != Game::LEFT) game.dir = Game::RIGHT; break;
  case 'w': if (game.dir != Game::DOWN) game.dir = Game::UP; break;
  case 's': if (game.dir != Game::UP) game.dir = Game::DOWN; break;
  case 'x': game.gameOver = true; break; // Выход из игры
  }
 }
}

// Логика игры
void Logic(Game& game)
{
 int prevX = game.snakeX[0]; // Предыдущая позиция головы
 int prevY = game.snakeY[0];
 int prev2X, prev2Y;

 // Движение головы
 switch (game.dir)
 {
 case Game::LEFT: game.x--; break;
 case Game::RIGHT: game.x++; break;
 case Game::UP: game.y--; break;
 case Game::DOWN: game.y++; break;
 }

 // Проверка столкновения с границами
 if (game.x < 0  game.x >= width  game.y < 0 || game.y >= height)
  game.gameOver = true;

 // Проверка столкновения с телом
 for (int i = 1; i < game.snakeLength; i++)
 {
  if (game.snakeX[i] == game.x && game.snakeY[i] == game.y)
   game.gameOver = true;
 }

 // Проверка съедания фрукта
 if (game.x == game.fruitX && game.y == game.fruitY)
 {
  game.score += 10; // Увеличение счета
  game.fruitX = rand() % width; // Новый фрукт
  game.fruitY = rand() % height;

  if (game.snakeLength >= game.snakeCapacity) // Расширение при необходимости
   ExpandSnake(game);

  game.snakeX[game.snakeLength] = -1; // Добавление нового сегмента
  game.snakeY[game.snakeLength] = -1;
  game.snakeLength++; // Увеличение длины
 }

 // Обновление позиций сегментов
 game.snakeX[0] = game.x;
 game.snakeY[0] = game.y;
 for (int i = 1; i < game.snakeLength; i++)
 {
  prev2X = game.snakeX[i];
  prev2Y = game.snakeY[i];
  game.snakeX[i] = prevX;
  game.snakeY[i] = prevY;
  prevX = prev2X;
  prevY = prev2Y;
 }
}

int main() {
 setlocale(LC_ALL, "RUS"); // Устанавливаем для корректного отображения текста на русском
 srand(time(0)); // Инициализация генератора случайных чисел
 Game game; // Создание экземпляра игры
 InitGame(game); // Инициализация игры

 // Главный игровой цикл
 while (!game.gameOver)
 {
  Draw(game); // Отрисовка
  Input(game); // Ввод
  Logic(game); // Логика
  Sleep(100);  // Задержка (скорость игры)
 }

 // Освобождение памяти
 delete[] game.snakeX;
 delete[] game.snakeY;

 // Вывод итогового счета
 COORD pos = { 0, height + 3 };
 SetConsoleCursorPosition(game.hConsole, pos);
 cout << "Конец игры! Итоговый счёт: " << game.score;

 Sleep(2000); // Задержка перед выходом
 return 0;
}
