/*
 * File: Breakout.java
 * -------------------
 * Name:
 * Section Leader:
 * 
 * This file will eventually implement the game of Breakout.
 */

import acm.graphics.*;
import acm.program.*;
import acm.util.*;

import java.applet.*;
import java.awt.*;
import java.awt.event.*;

import org.w3c.dom.css.Counter;


public class Breakout extends GraphicsProgram {

	/** Width and height of application window in pixels */
	public static final int APPLICATION_WIDTH = 400;
	public static final int APPLICATION_HEIGHT = 600;

	/** Dimensions of game board (usually the same) */
	private static final int WIDTH = APPLICATION_WIDTH;
	private static final int HEIGHT = APPLICATION_HEIGHT;

	/** Dimensions of the paddle */
	private static final int PADDLE_WIDTH = 60;
	private static final int PADDLE_HEIGHT = 10;

	/** Offset of the paddle up from the bottom */
	private static final int PADDLE_Y_OFFSET = 30;

	/** Number of bricks per row */
	private static final int NBRICKS_PER_ROW = 10;

	/** Number of rows of bricks */
	private static final int NBRICK_ROWS = 10;

	/** Separation between bricks */
	private static final int BRICK_SEP = 4;

	/** Width of a brick */
	private static final int BRICK_WIDTH = (WIDTH - (NBRICKS_PER_ROW - 1) * BRICK_SEP) / NBRICKS_PER_ROW;

	/** Height of a brick */
	private static final int BRICK_HEIGHT = 8;

	/** Radius of the ball in pixels */
	private static final int BALL_RADIUS = 10;

	/** Offset of the top brick row from the top */
	private static final int BRICK_Y_OFFSET = 70;

	/** Number of turns */
	private static final int NTURNS = 3;

	/* Method: run() */
	/** Runs the Breakout program. */
	private void Draw() {
		addMouseListeners();
		drawbricks();
		drawpaddle();
		drawball();
	}

	private void Game() {
		waitForClick();
		BallSpeed();
		while (true) {
			moveBall();
			/* stops the program if ball collides with lower wall */
			if (ball.getY() >= getHeight()) {
				break;
			}
			/* if no bricks present, the program stops */
			if (g == 0) {
				break;
			}

		}
	}
	/* addes collider */
	private GObject getCollidingObject() {
		if((getElementAt(ball.getX(), ball.getY())) != null) {
	         return getElementAt(ball.getX(), ball.getY());
	      }
		else if (getElementAt( (ball.getX() + BALL_RADIUS*2), ball.getY()) != null ){
	         return getElementAt(ball.getX() + BALL_RADIUS*2, ball.getY());
	      }
		else if(getElementAt(ball.getX(), (ball.getY() + BALL_RADIUS*2)) != null ){
	         return getElementAt(ball.getX(), ball.getY() + BALL_RADIUS*2);
	      }
		else if(getElementAt((ball.getX() + BALL_RADIUS*2), (ball.getY() + BALL_RADIUS*2)) != null ){
	         return getElementAt(ball.getX() + BALL_RADIUS*2, ball.getY() + BALL_RADIUS*2);
	      }
		else{
	         return null;
	      }
	}

	/* gives the ball speed vy = 4.0 and vx is random between 1.0 and 3.0 */
	private void BallSpeed() {
		vy = 4.0;
		vx = rgen.nextDouble(1.0, 3.0);
		if (rgen.nextBoolean(0.5)) {
			vx = -vx;
		}

	}

	/* number of tries */
	private int i = 3;

	/* moves the ball and decides when ball has to collide */
	private void moveBall() {
		while (true) {
			pause(10);
			ball.move(vx, vy);
			GObject collider = getCollidingObject();
			/*
			 * if ball hits the paddle, paddle doesn't disappear and ball changes direction
			 * from vy to -vy
			 */
			if (collider == paddle) {
				vy = -vy;
			}
			/* if ball hits right and left wall, ball changes direction from vx to -vx */
			if (ball.getX() > WIDTH - 2 * BALL_RADIUS) {
				vx = -vx;
			}
			if (ball.getX() < 0) {
				vx = -vx;
			}
			/* if ball hits the upper wall, it changes direction from vy to -vy */
			if (ball.getY() < 0) {
				vy = -vy;
			}
			/*
			 * if player misses the ball, it spawns in the centre again and continues moving
			 */
			if (ball.getY() > HEIGHT - 2 * BALL_RADIUS) {
				ball.setLocation(getWidth() / 2 - BALL_RADIUS, getHeight() / 2 - BALL_RADIUS);
				i--;
			}
			/*
			 * if ball hits the object that isnt paddle(in our situation it's a brick), it
			 * changes direction from vy to -vy and brick disappears
			 */
			if (collider != paddle & collider != null) {
				vy = -vy;
				g--;
				remove(collider);
			}
			if (i == 0) {
				removeAll();
				break;
			}
	}
	}

	private GRect brick;
	private GOval ball;
	private GRect paddle;
	private double vx, vy;
	private RandomGenerator rgen = RandomGenerator.getInstance();

	/* draws 10 rows of bricks. In each row are 10 bricks(totally 100) */
	private void drawbricks() {
		for (int y = 0; y <= 9; y++) {
			for (int x = 0; x <= 9; x++) {
				brick = new GRect(4 * x + x * BRICK_WIDTH, BRICK_Y_OFFSET + 4 * y + y * BRICK_HEIGHT, BRICK_WIDTH,
						BRICK_HEIGHT);
				brick.setFilled(true);
				if (y < 2) {
					brick.setFillColor(Color.RED);
				}
				if (y >= 2 & y < 4) {
					brick.setFillColor(Color.ORANGE);
				}
				if (y >= 4 & y < 6) {
					brick.setFillColor(Color.YELLOW);
				}
				if (y >= 6 & y < 8) {
					brick.setFillColor(Color.GREEN);
				}
				if (y >= 8) {
					brick.setFillColor(Color.CYAN);
				}
				add(brick);
			}
		}
	}

	/* draws a paddle */
	private void drawpaddle() {
		paddle = new GRect(getWidth() / 2 - PADDLE_WIDTH / 2, getHeight() - 30, PADDLE_WIDTH, PADDLE_HEIGHT);
		paddle.setFilled(true);
		add(paddle);
	}

	/* draws a ball */
	private void drawball() {
		ball = new GOval(getWidth() / 2 - BALL_RADIUS, getHeight() / 2 - BALL_RADIUS, 2 * BALL_RADIUS, 2 * BALL_RADIUS);
		ball.setFilled(true);
		add(ball);
	}

	public void run() {
		Draw();
		Game();
		if (g == 0) {
			/* everything disappears after finishing the game */
			removeAll();
		}
		pause(10);
	}

	/* number of bricks */
	private int g = 100;

	/* links paddle's coordinates to the mouse's coordinates */
	public void mouseMoved(MouseEvent a) {
		/* makes sure that paddle won't move if mouse is outside the game */
		if (a.getX() < 400 - PADDLE_WIDTH) {
			paddle.setLocation(a.getX(), getHeight() - 30);
		}
	}
}
