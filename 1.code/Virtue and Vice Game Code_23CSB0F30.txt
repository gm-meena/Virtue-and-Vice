import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;
import java.util.Random;

public class VirtueAndViceGame extends JPanel implements ActionListener, KeyListener {
    private Timer gameTimer, colorChangeTimer;
    private int playerX, playerY;
    private int playerSpeed = 5;
    private ArrayList<Rectangle> projectiles;
    private ArrayList<Bar> fallingBars;
    private int barSpeed = 1;
    private int score = 0;
    private int lives = 3; // Reduced the number of lives to 3
    private boolean gameOver = false;
    private boolean gameStarted = false;
    private Color backgroundColor = Color.BLACK;
    private Color projectileColor = Color.CYAN;
    private Color shooterColor = Color.YELLOW;
    private String popUpMessage = "";
    private Random random = new Random();
    private JButton startButton, pauseButton;
    private int effectCounter = 0;
    private String currentEffect = "Clouds"; // Starting with clouds
    private Color[] shooterBallColors = {Color.RED, Color.GREEN, Color.BLUE, Color.MAGENTA, Color.CYAN};
    private int colorIndex = 0;

    public VirtueAndViceGame() {
        this.setPreferredSize(new Dimension(800, 600));
        this.setFocusable(true);
        this.addKeyListener(this);

        // Initialize player position
        playerX = 375;
        playerY = 550;

        // Initialize projectiles and bars
        projectiles = new ArrayList<>();
        fallingBars = new ArrayList<>();
        generateBars();

        // Game timer (for projectiles and bar movement)
        gameTimer = new Timer(20, this);

        // Color change timer (every 4 seconds for effect change and color change)
        colorChangeTimer = new Timer(4000, e -> changeVisualEffectAndColor());
        
        // Initialize start and pause buttons
        startButton = new JButton("Start");
        startButton.setBounds(50, 500, 100, 40);
        startButton.addActionListener(e -> startGame());

        pauseButton = new JButton("Pause");
        pauseButton.setBounds(700, 10, 100, 40);
        pauseButton.addActionListener(e -> pauseGame());
        pauseButton.setVisible(false);  // Initially hidden until the game starts

        this.setLayout(null);
        this.add(startButton);
        this.add(pauseButton);
    }

    @Override
    public void paintComponent(Graphics g) {
        super.paintComponent(g);

        if (gameStarted) {
            setBackground(backgroundColor);

            // Draw the visual effect (clouds, rain, or stars)
            drawBackgroundElements(g);

            if (gameOver) {
                g.setColor(Color.RED);
                g.setFont(new Font("Arial", Font.BOLD, 40));
                g.drawString("GAME OVER", 300, 300);
                g.setFont(new Font("Arial", Font.PLAIN, 20));
                g.drawString("Score: " + score, 350, 350);
            } else {
                // Draw player (cannon)
                g.setColor(shooterColor);
                g.fillRect(playerX, playerY, 50, 20); // Cannon base
                g.fillRect(playerX + 15, playerY - 10, 20, 10); // Cannon barrel (cannon's firing part)

                // Draw projectiles (positive energy beams)
                for (Rectangle p : projectiles) {
                    g.setColor(projectileColor); // Make projectile the same color as the shooter
                    g.fillRect(p.x, p.y, 5, 10);
                }

                // Draw falling bars (positive/negative)
                for (Bar bar : fallingBars) {
                    g.setColor(bar.isPositive() ? Color.GREEN : Color.RED);
                    g.fillRect(bar.x, bar.y, bar.name.length() * 12, 20); // Adjust width to fit the text
                    g.setColor(Color.BLACK);
                    g.drawString(bar.name, bar.x + 5, bar.y + 15);
                }

                // Draw score and lives
                g.setColor(Color.WHITE);
                g.drawString("Score: " + score, 10, 20);
                g.drawString("Lives: " + lives, 10, 40);

                // Display pop-up messages (positive feedback)
                if (!popUpMessage.isEmpty()) {
                    g.setFont(new Font("Arial", Font.BOLD, 30));
                    g.setColor(Color.YELLOW);
                    g.drawString(popUpMessage, 300, 250);
                }
            }
        }
    }

    private void drawBackgroundElements(Graphics g) {
        // Display clouds, rain, or stars based on currentEffect
        if (currentEffect.equals("Clouds")) {
            backgroundColor = new Color(135, 206, 235); // Sky Blue for clouds
            g.fillRect(0, 0, getWidth(), getHeight()); // Set the background to Sky Blue

            // Cumulus clouds (larger, rounded shapes)
            g.setColor(new Color(255, 255, 255, 180)); // White with some transparency for cloud effect
            g.fillOval(50, 50, 150, 100);  // Cumulus cloud shape
            g.fillOval(200, 80, 180, 120); // Larger cumulus cloud
            g.fillOval(500, 120, 160, 110); // Another cumulus cloud
            g.fillOval(350, 150, 140, 90);  // Smaller cumulus cloud
        } else if (currentEffect.equals("Rain")) {
            backgroundColor = Color.BLACK; // Black background for rain
            g.setColor(Color.BLUE); // Blue rain color
            for (int i = 0; i < 50; i++) {
                int x = random.nextInt(getWidth());
                int y = random.nextInt(getHeight());
                g.drawLine(x, y, x, y + 10); // Simulating rain with blue lines
            }
        } else if (currentEffect.equals("Stars")) {
            backgroundColor = new Color(25, 25, 112); // Night Sky color (Dark Blue)
            g.setColor(Color.WHITE);
            for (int i = 0; i < 100; i++) {
                int x = random.nextInt(getWidth());
                int y = random.nextInt(getHeight());
                g.fillOval(x, y, 2, 2); // Drawing stars
            }
        }
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (!gameStarted || gameOver) return;

        // Move projectiles (positive energy beams)
        ArrayList<Rectangle> toRemoveProjectiles = new ArrayList<>();
        for (Rectangle p : projectiles) {
            p.y -= 5;
            if (p.y < 0) {
                // If the projectile goes off-screen (misses a bar), reduce life
                toRemoveProjectiles.add(p);
                lives--;
                popUpMessage = "Missed!";
            }
        }
        projectiles.removeAll(toRemoveProjectiles);

        // Move falling bars (positive/negative)
        ArrayList<Bar> toRemoveBars = new ArrayList<>();
        for (Bar bar : fallingBars) {
            bar.y += barSpeed;  // Slow down the falling speed of bars
            if (bar.y > playerY) {
                toRemoveBars.add(bar);
            }
        }
        fallingBars.removeAll(toRemoveBars);

        // Collision detection: positive energy beams hitting falling bars
        ArrayList<Bar> toRemoveBarsForCollision = new ArrayList<>();
        ArrayList<Rectangle> toRemoveProjectilesForCollision = new ArrayList<>();
        for (Rectangle p : projectiles) {
            for (Bar bar : fallingBars) {
                // Create a Rectangle for each Bar to handle collision
                Rectangle barRect = new Rectangle(bar.x, bar.y, bar.name.length() * 12, 20);
                if (p.intersects(barRect)) {
                    toRemoveProjectilesForCollision.add(p);
                    toRemoveBarsForCollision.add(bar);
                    if (bar.isPositive()) {
                        score += 10;
                        popUpMessage = generatePositiveMessage(bar.name);
                    } else {
                        lives--;  // Lost a life due to hitting a bad bar
                        popUpMessage = generateNegativeMessage(bar.name);
                    }
                }
            }
        }
        projectiles.removeAll(toRemoveProjectilesForCollision);
        fallingBars.removeAll(toRemoveBarsForCollision);

        // Generate more bars if all bars are removed
        if (fallingBars.isEmpty()) {
            generateBars();
        }

        // Check game over condition
        if (lives <= 0) {
            gameOver = true;
        }

        repaint();
    }

    // Method to generate positive feedback messages
    private String generatePositiveMessage(String barName) {
        switch (barName) {
            case "Love":
                return "Spread the Love!";
            case "Compassion":
                return "Compassion Wins!";
            case "Empathy":
                return "Empathy is Power!";
            case "Kindness":
                return "Keep Being Kind!";
            case "Hope":
                return "Hope Always!";
            case "Happiness":
                return "Joyful Victory!";
            default:
                return "Good Job!";
        }
    }

    // Method to generate negative feedback messages
    private String generateNegativeMessage(String barName) {
        switch (barName) {
            case "Anger":
                return "Anger Never Wins!";
            case "Greed":
                return "Greed Only Hurts!";
            case "Hatred":
                return "Let Go of Hatred!";
            case "Envy":
                return "Envy Destroys You!";
            case "Jealousy":
                return "Jealousy is a Trap!";
            case "Sadness":
                return "Overcome the Sadness!";
            case "Fear":
                return "Don't Let Fear Win!";
            default:
                return "Oops!";
        }
    }

    private void generateBars() {
        int rows = 5;
        int cols = 8; // Adjust the number of columns for more bars
        int margin = 20;
        int verticalSpacing = 50;

        for (int row = 0; row < rows; row++) {
            for (int col = 0; col < cols; col++) {
                int x = col * (getWidth() / cols) + margin;
                int y = row * verticalSpacing + 30;
                String barName = generateBarName();
                fallingBars.add(new Bar(x, y, barName));
            }
        }
    }

    private String generateBarName() {
        String[] positiveNames = {"Love", "Empathy", "Kindness", "Compassion", "Hope", "Happiness"};
        String[] negativeNames = {"Anger", "Greed", "Hatred", "Envy", "Jealousy", "Sadness", "Fear"};
        return random.nextBoolean() ? positiveNames[random.nextInt(positiveNames.length)] :
                negativeNames[random.nextInt(negativeNames.length)];
    }

    private void changeVisualEffectAndColor() {
        effectCounter++;
        if (effectCounter % 3 == 0) {
            currentEffect = "Clouds";
        } else if (effectCounter % 3 == 1) {
            currentEffect = "Rain";
        } else {
            currentEffect = "Stars";
        }

        // Change shooter and ball color to match the effect color
        if (currentEffect.equals("Clouds")) {
            shooterColor = new Color(135, 206, 235); // Sky Blue
            projectileColor = new Color(135, 206, 235); // Sky Blue
        } else if (currentEffect.equals("Rain")) {
            shooterColor = Color.BLUE; // Rain Blue
            projectileColor = Color.BLUE; // Rain Blue
        } else {
            shooterColor = Color.WHITE; // Stars White
            projectileColor = Color.WHITE; // Stars White
        }
    }

    @Override
    public void keyPressed(KeyEvent e) {
        if (!gameStarted || gameOver) return;

        int key = e.getKeyCode();
        if (key == KeyEvent.VK_LEFT && playerX > 0) {
            playerX -= playerSpeed;
        }
        if (key == KeyEvent.VK_RIGHT && playerX < getWidth() - 50) {
            playerX += playerSpeed;
        }
        if (key == KeyEvent.VK_SPACE) {
            // Shoot the ball from the cannon's barrel
            projectiles.add(new Rectangle(playerX + 25, playerY - 10, 5, 10));
        }
    }

    @Override
    public void keyReleased(KeyEvent e) {}

    @Override
    public void keyTyped(KeyEvent e) {}

    public void startGame() {
        if (!gameStarted) {
            gameStarted = true;
            gameTimer.start();
            colorChangeTimer.start();
            startButton.setVisible(false); // Hide Start button after starting
            pauseButton.setVisible(true); // Show Pause button
            generateBars();
        }
    }

    public void pauseGame() {
        if (gameStarted) {
            gameTimer.stop();
            colorChangeTimer.stop();
        }
    }

    public static void main(String[] args) {
        JFrame frame = new JFrame("Virtue and Vice Game");
        VirtueAndViceGame game = new VirtueAndViceGame();
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.add(game);
        frame.pack();
        frame.setVisible(true);
    }

    class Bar {
        int x, y;
        String name;
        boolean positive;

        public Bar(int x, int y, String name) {
            this.x = x;
            this.y = y;
            this.name = name;
            this.positive = name.equals("Love") || name.equals("Empathy") || name.equals("Kindness") ||
                             name.equals("Compassion") || name.equals("Hope") || name.equals("Happiness");
        }

        public boolean isPositive() {
            return positive;
        }
    }
}