# MyFlappyBird
Followed a YouTube tutorial


package flappyBird;

import javax.swing.JFrame;

import java.awt.Color;
import java.awt.Font;
import java.awt.Graphics;
import java.awt.Rectangle;

import javax.swing.Timer;

import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.awt.event.MouseEvent;
import java.awt.event.MouseListener;
import java.util.ArrayList;
import java.util.Random;

public class FlappyBird implements ActionListener, MouseListener, KeyListener {
	
	public static FlappyBird flappyBird;
	
	public final int width= 1200, height = 800;
	
	public Renderer renderer;
	
	public Rectangle bird;
	
	public int ticks, yMotion, score;
	
	public ArrayList<Rectangle> columns;
	
	public Random rand;
	
	public boolean gameOver, started;
	
	public FlappyBird() {
		
		JFrame jframe = new JFrame();
		Timer timer = new Timer(20, this);
		
		
		renderer = new Renderer();
		rand = new Random();
		
		jframe.add(renderer);
		jframe.setTitle("Flappy Bird");
		jframe.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		jframe.setSize(width, height);
		jframe.addMouseListener(this);
		jframe.addKeyListener(this);
		jframe.setResizable(false);
		jframe.setVisible(true);
		
		bird = new Rectangle(width/2-10, height/2-10, 20, 20);
		columns = new ArrayList<Rectangle>();
		
		addColumn(true);
		addColumn(true);
		addColumn(true);
		addColumn(true);
		
		timer.start();
		
	}
	
	public void addColumn(boolean start) {
		int space=300;
		int wid = 100;
		int heig = 50+rand.nextInt(300);
		
		if (start) {
			
		
		columns.add(new Rectangle(width+wid+columns.size() *300, height-heig-120, wid, heig));
		columns.add(new Rectangle(width+wid+(columns.size()-1)*300, 0, wid, height-heig-space));
		} else {
			columns.add(new Rectangle(columns.get(columns.size()-1).x + 600, height-heig-120, wid, heig));
			columns.add(new Rectangle(columns.get(columns.size()-1).x, 0, wid, height-heig-space));
		}
	}
	
	public void paintColumn (Graphics g, Rectangle column) {
		g.setColor(Color.green.darker());
		g.fillRect(column.x, column.y, column.width, column.height);
	}
	
	public void jump() {
		if (gameOver) {
			
			bird = new Rectangle(width/2-10, height/2-10, 20, 20);
			columns.clear();
			yMotion = 0;
			score = 0;
			
			addColumn(true);
			addColumn(true);
			addColumn(true);
			addColumn(true);
			
			gameOver = false;
		}
		if (!started ) {
			started = true;
		}
		else if (!gameOver) {
			if (yMotion > 0 )
			{
				yMotion = 0;
			}
			yMotion -= 10;
		}
	}
	
	public void actionPerformed (ActionEvent e) {
		
		int speed = 10;
		ticks++;
		
		if (started) {
			
		
		for (int i = 0; i < columns.size(); i++) {
			Rectangle column = columns.get(i);
			column.x -= speed;
		}
		
		if (ticks%2 == 0 && yMotion < 15) {
			yMotion += 2;
			
		}
		for (int i = 0; i < columns.size(); i++) {
			
			Rectangle column = columns.get(i);
			
			if (column.x +column.width <0) {
				columns.remove(column);
				
				if (column.y== 0) {
				addColumn(false);	
				}
				
				
			}
		}
		
		bird.y += yMotion;
		
		
		
		for (Rectangle column : columns) {
			
			if (bird.x + bird.width / 2 > column.x +column.width/2 - 10 && bird.x + bird.width / 2 < column.x + column.width / 2 + 10 && !gameOver) {
				score ++;
			}
			
			if (column.intersects(bird)|| bird.y <= 120) {
				
				gameOver = true;
				
				if (bird.x <= column.x) {
					
					bird.x = column.x - bird.width;
					
				}
				else {
					if (column.y != 0 ) {
						bird.y = column.y - bird.height;
					}
					else if (bird.y < column.height) {
						bird.y = column.height;
					}
				}
				
				
				bird.x = column.x - bird.width;
					
				}
		}
		if (bird.y > height - 120 || bird.y <0) {
			gameOver = true;
		}
				
		
		if (bird.y +yMotion >= height - 120) {
			bird.y = height-120 - bird.height;
			gameOver = true;
		}
	}
		renderer.repaint();
	}
	
	
	
	public void repaint(Graphics g) {
			
		g.setColor(Color.cyan);
		g.fillRect(0, 0, width, height);
		
		g.setColor(Color.orange);
		g.fillRect(0, height-120, width, 120);
		
		g.setColor(Color.green);
		g.fillRect(0, height-120, width, 20);
		
		g.setColor(Color.magenta);
		g.fillRect(bird.x, bird.y, bird.width, bird.height);
		
		for (Rectangle column : columns) {
			paintColumn (g, column );
		}
		
		g.setColor(Color.white);
		g.setFont(new Font("Arial", 1, 100));
		
		if (!started) {
			
			g.drawString("Click to start", 300, height/2-50);
		}
		if (gameOver) {
			g.drawString("Game Over...", 300, height/2-50);
			g.drawString("SCORE: "+(score/2), 300, 500);
		}
		
		if (!gameOver && started) {
			g.drawString(String.valueOf(score/2), width / 2, 100);
		}
		
		
	}
	
	public static void main (String [] args) {
		
		flappyBird= new FlappyBird();
		
}

	@Override
	public void mouseClicked(MouseEvent e) {
		// TODO Auto-generated method stub
		jump();
	}

	@Override
	public void mousePressed(MouseEvent e) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void mouseReleased(MouseEvent e) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void mouseEntered(MouseEvent e) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void mouseExited(MouseEvent e) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void keyTyped(KeyEvent e) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void keyPressed(KeyEvent e) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void keyReleased(KeyEvent e) {
		// TODO Auto-generated method stub
		if (e.getKeyCode() == KeyEvent.VK_SPACE) {
			jump();
		}
	}
	
}
