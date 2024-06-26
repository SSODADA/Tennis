# Tennis

https://github.com/SSODADA/Tennis/assets/80105027/349a60cb-f2e3-4d32-a758-cbd8d865c57e

## 추가한 기능
1. 배경 음악 추가 
2. 중앙 상단에 점수 표시 
3. 공이 패달에 닿을시 속도 증가
4. 공이 패달에 닿을시 색 변경

## 과제 코드 
         ////////////////////////////////////////////////////////////
         // Headers
         ////////////////////////////////////////////////////////////
         #include <SFML/Graphics.hpp>
         #include <SFML/Audio.hpp>
         #include <cmath>
         #include <ctime>
         #include <cstdlib>
         
         #ifdef SFML_SYSTEM_IOS
         #include <SFML/Main.hpp>
         #endif
         
         std::string resourcesDir()
         {
         #ifdef SFML_SYSTEM_IOS
             return "";
         #else
             return "resources/";
         #endif
         }
         
         ////////////////////////////////////////////////////////////
         /// Entry point of application
         ///
         /// \return Application exit code
         ///
         ////////////////////////////////////////////////////////////
         void resetGame(sf::RectangleShape& leftPaddle, sf::RectangleShape& rightPaddle, sf::CircleShape& ball, float gameWidth, float gameHeight, float paddleSize, float ballRadius, float paddleSpeed)
         {
             leftPaddle.setPosition(10.f + paddleSize / 2.f, gameHeight / 2.f);
             rightPaddle.setPosition(gameWidth - 10.f - paddleSize / 2.f, gameHeight / 2.f);
             ball.setPosition(gameWidth / 2.f, gameHeight / 2.f);
         
             // Reset the ball angle
             float pi = 3.14159f;
             float ballAngle = static_cast<float>(std::rand() % 360) * 2.f * pi / 360.f;
             while (std::abs(std::cos(ballAngle)) < 0.7f)
             {
                 ballAngle = static_cast<float>(std::rand() % 360) * 2.f * pi / 360.f;
             }
         }
         
         int main()
         {
             std::srand(static_cast<unsigned int>(std::time(NULL)));
         
             // Define some constants
             const float pi = 3.14159f;
             const float gameWidth = 800;
             const float gameHeight = 600;
             sf::Vector2f paddleSize(25, 100);
             float ballRadius = 10.f;
         
             // Create the window of the application
             sf::RenderWindow window(sf::VideoMode(static_cast<unsigned int>(gameWidth), static_cast<unsigned int>(gameHeight), 32), "SFML Tennis",
                 sf::Style::Titlebar | sf::Style::Close);
             window.setVerticalSyncEnabled(true);
         
             // Load the sounds used in the game
             sf::SoundBuffer ballSoundBuffer;
             if (!ballSoundBuffer.loadFromFile(resourcesDir() + "ball.wav"))
                 return EXIT_FAILURE;
             sf::Sound ballSound(ballSoundBuffer);
         
             // Load the background music
             sf::Music backgroundMusic;
             if (!backgroundMusic.openFromFile(resourcesDir() + "magical-wonderland-of-smiles-ai-205292.wav"))
                 backgroundMusic.setLoop(true);
             backgroundMusic.play();
         
         
             // Create the SFML logo texture:
             sf::Texture sfmlLogoTexture;
             if (!sfmlLogoTexture.loadFromFile(resourcesDir() + "sfml_logo.png"))
                 return EXIT_FAILURE;
             sf::Sprite sfmlLogo;
             sfmlLogo.setTexture(sfmlLogoTexture);
             sfmlLogo.setPosition(170, 50);
         
             // Create the left paddle
             sf::RectangleShape leftPaddle;
             leftPaddle.setSize(paddleSize - sf::Vector2f(3, 3));
             leftPaddle.setOutlineThickness(3);
             leftPaddle.setOutlineColor(sf::Color::Black);
             leftPaddle.setFillColor(sf::Color(100, 100, 200));
             leftPaddle.setOrigin(paddleSize / 2.f);
         
             // Create the right paddle
             sf::RectangleShape rightPaddle;
             rightPaddle.setSize(paddleSize - sf::Vector2f(3, 3));
             rightPaddle.setOutlineThickness(3);
             rightPaddle.setOutlineColor(sf::Color::Black);
             rightPaddle.setFillColor(sf::Color(200, 100, 100));
             rightPaddle.setOrigin(paddleSize / 2.f);
         
             // Create the ball
             sf::CircleShape ball;
             ball.setRadius(ballRadius - 3);
             ball.setOutlineThickness(2);
             ball.setOutlineColor(sf::Color::Black);
             ball.setFillColor(sf::Color::White);
             ball.setOrigin(ballRadius / 2, ballRadius / 2);
         
             // Load the text font
             sf::Font font;
             if (!font.loadFromFile(resourcesDir() + "tuffy.ttf"))
                 return EXIT_FAILURE;
         
             // Initialize the pause message
             sf::Text pauseMessage;
             pauseMessage.setFont(font);
             pauseMessage.setCharacterSize(40);
             pauseMessage.setPosition(170.f, 200.f);
             pauseMessage.setFillColor(sf::Color::White);
         
         #ifdef SFML_SYSTEM_IOS
             pauseMessage.setString("Welcome to SFML Tennis!\nTouch the screen to start the game.");
         #else
             pauseMessage.setString("Welcome to SFML Tennis!\n\nPress space to start the game.");
         #endif
         
             // Define the paddles properties
             sf::Clock AITimer;
             const sf::Time AITime = sf::seconds(0.1f);
             const float paddleSpeed = 400.f;
             float rightPaddleSpeed = 0.f;
             float ballSpeed = 400.f;
             float ballAngle = 0.f; // to be changed later
             int playerscore = 0;
             int computerscore = 0;
             int maxscore = 5;
         
             sf::Clock clock;
         
             sf::Text scoreText;
             scoreText.setFont(font);
             scoreText.setCharacterSize(30);
             scoreText.setFillColor(sf::Color::White);
             scoreText.setPosition(gameWidth / 2.f - 50.f, 10.f);
         
             bool isPlaying = false;
             while (window.isOpen())
             {
                 // Handle events
                 sf::Event event;
                 while (window.pollEvent(event))
                 {
                     // Update scoreText to display scores
                     scoreText.setString(std::to_string(playerscore) + " : " + std::to_string(computerscore));
         
                     // Clear the window
                     window.clear(sf::Color(50, 50, 50));
         
                     if (isPlaying)
                     {
                         // Draw the paddles, ball, and scores
                         window.draw(leftPaddle);
                         window.draw(rightPaddle);
                         window.draw(ball);
                         window.draw(scoreText);
                         // Check the collisions between the ball and the paddles
                     }
                     else
                     {
                         // Draw the pause message and SFML logo
                         window.draw(pauseMessage);
                         window.draw(sfmlLogo);
                     }
         
                     // Window closed or escape key pressed: exit
                     if ((event.type == sf::Event::Closed) ||
                         ((event.type == sf::Event::KeyPressed) && (event.key.code == sf::Keyboard::Escape)))
                     {
                         window.close();
                         break;
                     }
         
                     // Space key pressed: play
                     if (((event.type == sf::Event::KeyPressed) && (event.key.code == sf::Keyboard::Space)) ||
                         (event.type == sf::Event::TouchBegan))
                     {
                         if (!isPlaying)
                         {
                             // (re)start the game
                             isPlaying = true;
                             clock.restart();
         
                             // Reset the position of the paddles and ball
                             leftPaddle.setPosition(10.f + paddleSize.x / 2.f, gameHeight / 2.f);
                             rightPaddle.setPosition(gameWidth - 10.f - paddleSize.x / 2.f, gameHeight / 2.f);
                             ball.setPosition(gameWidth / 2.f, gameHeight / 2.f);
         
                             // Reset the ball angle
                             do
                             {
                                 // Make sure the ball initial angle is not too much vertical
                                 ballAngle = static_cast<float>(std::rand() % 360) * 2.f * pi / 360.f;
                             } while (std::abs(std::cos(ballAngle)) < 0.7f);
                         }
                     }
         
                     // Window size changed, adjust view appropriately
                     if (event.type == sf::Event::Resized)
                     {
                         sf::View view;
                         view.setSize(gameWidth, gameHeight);
                         view.setCenter(gameWidth / 2.f, gameHeight / 2.f);
                         window.setView(view);
                     }
                 }
         
                 if (isPlaying)
                 {
                     // Left Paddle
                     if (ball.getPosition().x - ballRadius < leftPaddle.getPosition().x + paddleSize.x / 2 &&
                         ball.getPosition().x - ballRadius > leftPaddle.getPosition().x &&
                         ball.getPosition().y + ballRadius >= leftPaddle.getPosition().y - paddleSize.y / 2 &&
                         ball.getPosition().y - ballRadius <= leftPaddle.getPosition().y + paddleSize.y / 2)
                     {
                         // Increase ball speed
                         ballSpeed += 50.f;
         
                         // Change ball color to brighter
                         ball.setFillColor(sf::Color::Yellow);
         
                         if (ball.getPosition().y > leftPaddle.getPosition().y)
                             ballAngle = pi - ballAngle + static_cast<float>(std::rand() % 20) * pi / 180;
                         else
                             ballAngle = pi - ballAngle - static_cast<float>(std::rand() % 20) * pi / 180;
         
                         ballSound.play();
                         ball.setPosition(leftPaddle.getPosition().x + ballRadius + paddleSize.x / 2 + 0.1f, ball.getPosition().y);
                     }
         
                     // Right Paddle
                     if (ball.getPosition().x + ballRadius > rightPaddle.getPosition().x - paddleSize.x / 2 &&
                         ball.getPosition().x + ballRadius < rightPaddle.getPosition().x &&
                         ball.getPosition().y + ballRadius >= rightPaddle.getPosition().y - paddleSize.y / 2 &&
                         ball.getPosition().y - ballRadius <= rightPaddle.getPosition().y + paddleSize.y / 2)
                     {
                         // Increase ball speed
                         ballSpeed += 50.f;
         
                         // Change ball color to brighter
                         ball.setFillColor(sf::Color::Yellow);
         
                         if (ball.getPosition().y > rightPaddle.getPosition().y)
                             ballAngle = pi - ballAngle + static_cast<float>(std::rand() % 20) * pi / 180;
                         else
                             ballAngle = pi - ballAngle - static_cast<float>(std::rand() % 20) * pi / 180;
         
                         ballSound.play();
                         ball.setPosition(rightPaddle.getPosition().x - ballRadius - paddleSize.x / 2 - 0.1f, ball.getPosition().y);
                     }
         
                     if (playerscore == maxscore || computerscore == maxscore)
                     {
                         // Display the winner
                         isPlaying = false;
                         if (playerscore >= maxscore)
                             pauseMessage.setString("You Won!\n\nPress esc exit.");
                         else
                             pauseMessage.setString("You Lost!\n\nPress esc exit.");
                     }
         
         
                     float deltaTime = clock.restart().asSeconds();
         
                     // Move the player's paddle
                     if (sf::Keyboard::isKeyPressed(sf::Keyboard::Up) &&
                         (leftPaddle.getPosition().y - paddleSize.y / 2 > 5.f))
                     {
                         leftPaddle.move(0.f, -paddleSpeed * deltaTime);
                     }
                     if (sf::Keyboard::isKeyPressed(sf::Keyboard::Down) &&
                         (leftPaddle.getPosition().y + paddleSize.y / 2 < gameHeight - 5.f))
                     {
                         leftPaddle.move(0.f, paddleSpeed * deltaTime);
                     }
         
                     if (sf::Touch::isDown(0))
                     {
                         sf::Vector2i pos = sf::Touch::getPosition(0);
                         sf::Vector2f mappedPos = window.mapPixelToCoords(pos);
                         leftPaddle.setPosition(leftPaddle.getPosition().x, mappedPos.y);
                     }
         
                     // Move the computer's paddle
                     if (((rightPaddleSpeed < 0.f) && (rightPaddle.getPosition().y - paddleSize.y / 2 > 5.f)) ||
                         ((rightPaddleSpeed > 0.f) && (rightPaddle.getPosition().y + paddleSize.y / 2 < gameHeight - 5.f)))
                     {
                         rightPaddle.move(0.f, rightPaddleSpeed * deltaTime);
                     }
         
                     // Update the computer's paddle direction according to the ball position
                     if (AITimer.getElapsedTime() > AITime)
                     {
                         AITimer.restart();
                         if (ball.getPosition().y + ballRadius > rightPaddle.getPosition().y + paddleSize.y / 2)
                             rightPaddleSpeed = paddleSpeed;
                         else if (ball.getPosition().y - ballRadius < rightPaddle.getPosition().y - paddleSize.y / 2)
                             rightPaddleSpeed = -paddleSpeed;
                         else
                             rightPaddleSpeed = 0.f;
                     }
         
                     // Move the ball
                     float factor = ballSpeed * deltaTime;
                     ball.move(std::cos(ballAngle) * factor, std::sin(ballAngle) * factor);
         
         #ifdef SFML_SYSTEM_IOS
                     const std::string inputString = "Touch the screen to restart.";
         #else
                     const std::string inputString = "Press space to restart or\nescape to exit.";
         #endif
         
                     // Check collisions between the ball and the screen
                     if (ball.getPosition().x - ballRadius < 0.f)
                     {
                         // Computer scores
                         computerscore++;
                         ballSpeed = 400.f;
                         ball.setFillColor(sf::Color::White);
                         resetGame(leftPaddle, rightPaddle, ball, gameWidth, gameHeight, paddleSize.y, ballRadius, paddleSpeed);
                         leftPaddle.setPosition(10.f + paddleSize.x / 2.f, gameHeight / 2.f);
                         rightPaddle.setPosition(gameWidth - 10.f - paddleSize.x / 2.f, gameHeight / 2.f);
                     }
                     if (ball.getPosition().x + ballRadius > gameWidth)
                     {
                         // Player scores
                         playerscore++;
                         ballSpeed = 400.f;
                         ball.setFillColor(sf::Color::White);
                         resetGame(leftPaddle, rightPaddle, ball, gameWidth, gameHeight, paddleSize.y, ballRadius, paddleSpeed);
                         leftPaddle.setPosition(10.f + paddleSize.x / 2.f, gameHeight / 2.f);
                         rightPaddle.setPosition(gameWidth - 10.f - paddleSize.x / 2.f, gameHeight / 2.f);
                     }
                     if (ball.getPosition().y - ballRadius < 0.f)
                     {
                         ballSound.play();
                         ballAngle = -ballAngle;
                         ball.setPosition(ball.getPosition().x, ballRadius + 0.1f);
                     }
                     if (ball.getPosition().y + ballRadius > gameHeight)
                     {
                         ballSound.play();
                         ballAngle = -ballAngle;
                         ball.setPosition(ball.getPosition().x, gameHeight - ballRadius - 0.1f);
                     }
         
                     // Check the collisions between the ball and the paddles
                     // Left Paddle
                     if (ball.getPosition().x - ballRadius < leftPaddle.getPosition().x + paddleSize.x / 2 &&
                         ball.getPosition().x - ballRadius > leftPaddle.getPosition().x &&
                         ball.getPosition().y + ballRadius >= leftPaddle.getPosition().y - paddleSize.y / 2 &&
                         ball.getPosition().y - ballRadius <= leftPaddle.getPosition().y + paddleSize.y / 2)
                     {
                         if (ball.getPosition().y > leftPaddle.getPosition().y)
                             ballAngle = pi - ballAngle + (std::rand() % 20) * pi / 180;
                         else
                             ballAngle = pi - ballAngle - (std::rand() % 20) * pi / 180;
         
                         ballSpeed += 50.f;
                         ball.setFillColor(sf::Color(std::rand() % 256, std::rand() % 256, std::rand() % 256));
                         ballSound.play();
                         ball.setPosition(leftPaddle.getPosition().x + ballRadius + paddleSize.x / 2 + 0.1f, ball.getPosition().y);
                     }
         
                     // Right Paddle
                     if (ball.getPosition().x + ballRadius > rightPaddle.getPosition().x - paddleSize.x / 2 &&
                         ball.getPosition().x + ballRadius < rightPaddle.getPosition().x &&
                         ball.getPosition().y + ballRadius >= rightPaddle.getPosition().y - paddleSize.y / 2 &&
                         ball.getPosition().y - ballRadius <= rightPaddle.getPosition().y + paddleSize.y / 2)
                     {
                         if (ball.getPosition().y > rightPaddle.getPosition().y)
                             ballAngle = pi - ballAngle + (std::rand() % 20) * pi / 180;
                         else
                             ballAngle = pi - ballAngle - (std::rand() % 20) * pi / 180;
         
                         ballSpeed += 50.f;
                         ball.setFillColor(sf::Color(std::rand() % 256, std::rand() % 256, std::rand() % 256));
                         ballSound.play();
                         ball.setPosition(rightPaddle.getPosition().x - ballRadius - paddleSize.x / 2 - 0.1f, ball.getPosition().y);
                     }
                 }
         
                 // Draw the score
                 scoreText.setString(std::to_string(playerscore) + " : " + std::to_string(computerscore));
                 scoreText.setPosition(gameWidth / 2.f - scoreText.getGlobalBounds().width / 2.f, 10.f);
         
                 // Clear the window
                 window.clear(sf::Color(50, 50, 50));
         
                 if (isPlaying)
                 {
                     // Draw the paddles and the ball
                     window.draw(leftPaddle);
                     window.draw(rightPaddle);
                     window.draw(ball);
                     window.draw(scoreText);
                 }
                 else
                 {
                     // Draw the pause message
                     window.draw(pauseMessage);
                     window.draw(sfmlLogo);
                 }
         
                 // Display things on screen
                 window.display();
             }
         
             return EXIT_SUCCESS;
         }
