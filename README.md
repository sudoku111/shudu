import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.PrintWriter;
import java.util.Scanner;

public class Sudoku extends JFrame {
    private JPanel[] pnlGame;
    private static JTextField[][][] txtGame;
    private JTextField lastFocusTextFiled;
    private JLabel time;
    private CountingThread thread = new CountingThread();
    private long programStart;
    private static boolean goOn=true;
    private static long elapsed;
    private static int count2=0;
    private static long timeRecord;
    private FocusListener textFocusListener = new FocusListener() {
        @Override
        public void focusGained(FocusEvent e) {
            lastFocusTextFiled = (JTextField) e.getComponent();
        }

        @Override
        public void focusLost(FocusEvent e) {
            lastFocusTextFiled = (JTextField) e.getComponent();
        }
    };
    private ButtonGroup group;
    private int count;
    private static int key;
    private static int t = 3;

    public Sudoku() {
        pnlGame = new JPanel[12];
        txtGame = new JTextField[9][3][3];
        gridInit();
    }

    public void gridInit() {
        this.setDefaultCloseOperation(this.EXIT_ON_CLOSE);
        this.setSize(800, 600);
        this.setResizable(false);
        this.setTitle("Suduko");
        this.setLayout(new GridLayout(3, 4)); // set layout

        for (int i = 0; i < 12; i++) {
            if ((i + 1) % 4 != 0) {
                pnlGame[i] = new JPanel();
                pnlGame[i].setBorder(BorderFactory.createLineBorder(Color.black)); // use border to draw the grid
                pnlGame[i].setLayout(new GridLayout(3, 3));
                this.add(pnlGame[i]);
            } else if (i == 3) {
            	JButton btn1 = new JButton("新游戏"); // 新游戏按钮
            	JButton btn2 = new JButton("存档"); //存档按钮
            	JButton btn3 = new JButton("读档"); //读档按钮
            	btn1.addActionListener(new ActionListener() {

            		@Override
            		public void actionPerformed(ActionEvent e) {
            			programStart = System.currentTimeMillis();
            			goOn=true;
            			if(count2==0) {
            				thread.start();
            				count2++;
            			}
            			String[][] n = new String[9][9];
            			count++;
            			try {
            				n = readfromFile(String.format("%d.txt", count)); // 从文件中读取题目
            			} catch (FileNotFoundException e1) {
            				e1.printStackTrace();
            			}

            			// fill some numbers randomly
            			for (int z = 0; z < 3; z++) {
            				for (int x = 0; x < 3; x++) {
            					for (int y = 0; y < 3; y++) {
            						if (n[x][y + 3 * z].equals("0")) {
            							txtGame[z][x][y].setText("");
            							txtGame[z][x][y].setEditable(true);
            						} else {
            							txtGame[z][x][y].setText(n[x][y + 3 * z]);
            							txtGame[z][x][y].setEditable(false);
            						}
            					}
            				}
            			}
            			for (int z = 3; z < 6; z++) {
            				for (int x = 0; x < 3; x++) {
            					for (int y = 0; y < 3; y++) {
            						if (n[x + 3][y + 3 * (z - 3)].equals("0")) {
            							txtGame[z][x][y].setText("");
            							txtGame[z][x][y].setEditable(true);
            						} else {
            							txtGame[z][x][y].setText(n[x + 3][y + 3 * (z - 3)]);
            							txtGame[z][x][y].setEditable(false);
            						}
            					}
            				}
            			}
            			for (int z = 6; z < 9; z++) {
            				for (int x = 0; x < 3; x++) {
            					for (int y = 0; y < 3; y++) {
            						if (n[x + 6][y + 3 * (z - 6)].equals("0")) {
            							txtGame[z][x][y].setText("");
            							txtGame[z][x][y].setEditable(true);
            						} else {
            							txtGame[z][x][y].setText(n[x + 6][y + 3 * (z - 6)]);
            							txtGame[z][x][y].setEditable(false);
            						}
            					}
            				}
            			}
            		}
            	});
            	btn2.addActionListener(new ActionListener() {
            		@Override
            		public void actionPerformed(ActionEvent e) {
            			try {
							SaveIntoFile(txtGame);
						} catch (FileNotFoundException e1) {
							e1.printStackTrace();
						}

            		}});
            	btn3.addActionListener(new ActionListener() {
            		@Override
            		public void actionPerformed(ActionEvent e) {
                        programStart = System.currentTimeMillis();
                        goOn=true;
            			String[][] n = new String[9][9];
            			try {
            				n=readfromFile("save.txt");
            			} catch (FileNotFoundException e1) {
            				// TODO Auto-generated catch block
            				e1.printStackTrace();
            			}
            			for (int z = 0; z < 3; z++) {
            				for (int x = 0; x < 3; x++) {
            					for (int y = 0; y < 3; y++) {
            						if (n[x][y + 3 * z].equals("0")) {
            							txtGame[z][x][y].setText("");
            							txtGame[z][x][y].setEditable(true);
            						} else {
            							txtGame[z][x][y].setText(n[x][y + 3 * z]);
            							txtGame[z][x][y].setEditable(false);
            						}
            					}
            				}
            			}
            			for (int z = 3; z < 6; z++) {
            				for (int x = 0; x < 3; x++) {
            					for (int y = 0; y < 3; y++) {
            						if (n[x + 3][y + 3 * (z - 3)].equals("0")) {
            							txtGame[z][x][y].setText("");
            							txtGame[z][x][y].setEditable(true);
            						} else {
            							txtGame[z][x][y].setText(n[x + 3][y + 3 * (z - 3)]);
            							txtGame[z][x][y].setEditable(false);
            						}
            					}
            				}
            			}
            			for (int z = 6; z < 9; z++) {
            				for (int x = 0; x < 3; x++) {
            					for (int y = 0; y < 3; y++) {
            						if (n[x + 6][y + 3 * (z - 6)].equals("0")) {
            							txtGame[z][x][y].setText("");
            							txtGame[z][x][y].setEditable(true);
            						} else {
            							txtGame[z][x][y].setText(n[x + 6][y + 3 * (z - 6)]);
            							txtGame[z][x][y].setEditable(false);
            						}
            					}
            				}
            			}
            		}});
            	pnlGame[i] = new JPanel();
            	pnlGame[i].add(btn2);
            	pnlGame[i].add(btn3);
            	pnlGame[i].add(btn1);
            	this.add(pnlGame[i]);
            } else if (i == 7) {
                group = new ButtonGroup();
                pnlGame[i] = new JPanel();
                addJRadioButton("易", i);
                addJRadioButton("中", i);
                addJRadioButton("难", i);
                time=new JLabel("00:00:00 000"); 
                pnlGame[i].add(time);
                
                this.add(pnlGame[i]);
            } else {

                pnlGame[i] = new JPanel();
                JButton btn3 = new JButton("提示"); // Create JButton object
                JTextField time = new JTextField();
                time.setText(String.valueOf(t));
                time.setEditable(false);
                pnlGame[i].add(time);
                btn3.addActionListener(new ActionListener() {
                    @Override
                    public void actionPerformed(ActionEvent e) {
                        if (lastFocusTextFiled == null) {
                            System.err.println("Please select a text field");
                            return;
                        }

                        if (t > 0) {
                            t--;
                            time.setEditable(true);
                            time.setText(String.valueOf(t));
                            time.setEditable(false);
                            String[][] n = new String[9][9];
                            try {
                                n = readfromFile(String.format("%d.txt", count + 30)); // 从文件中读取答案
                            } catch (FileNotFoundException e1) {
                                e1.printStackTrace();
                            }
                            //获得需要提示的txt位置
                            lastFocusTextFiled.setText("");//TODO
                            for (int z = 0; z < 9; z++) {
    							for (int x = 0; x < 3; x++) {
    								for (int y = 0; y < 3; y++) {
    									if (txtGame[z][x][y]==lastFocusTextFiled){
    										txtGame[z][x][y].setText(n[Cell.getX(z, x)][Cell.getY(z, y)]);
    										txtGame[z][x][y].setEditable(false);
    							}
    							}
    						}
    					}
                            

                        } else {
                            JDialog dialog = new JDialog(); // Create Frame
                            dialog.setBounds(300, 200, 300, 200);
                            dialog.setVisible(true);
                            JLabel jl2 = new JLabel("你的提示次数已用完，点击确定继续"); // create a label
                            final JButton but=new JButton("确定");
                            dialog.setModal(true);
                            dialog.setLayout(new FlowLayout());
                            dialog.add(jl2);
                            dialog.add(but);
                            but.addActionListener(new ActionListener() {
                                @Override
                                public void actionPerformed(ActionEvent e) {
                                	dialog.dispose();
                                }});
                        }
                    }
                });
                      
                pnlGame[i].add(btn3);
                this.add(pnlGame[i]);
            }

        }

        // 填数独
        int tt = 0;
        for (int z = 0; z < 9; z++) {
            if ((z + 1 + tt) % 4 == 0)
                tt++;
            for (int x = 0; x < 3; x++) {
                for (int y = 0; y < 3; y++) {
                    txtGame[z][x][y] = new JTextField();
                    txtGame[z][x][y].addFocusListener(textFocusListener);
                    txtGame[z][x][y].setBorder(BorderFactory.createEtchedBorder());
                    txtGame[z][x][y].setFont(new Font("Dialog", Font.ITALIC, 20));// Set size and font
                    txtGame[z][x][y].setHorizontalAlignment(JTextField.CENTER);// set position
                    pnlGame[z + tt].add(txtGame[z][x][y]);

                }
            }
        }

        
       
        
        this.setVisible(true);

    }

    public static void main(String[] args) throws FileNotFoundException {
    	
    	 new Sudoku();
    	 int mm=1;
    	while(mm>0) {
    		int mmm=0;
         while(end()) {
        	 mmm++;
         }
       
        if (!end()) {
        	goOn=false;
        	compareTime(elapsed);
        	JDialog dialog = new JDialog(); // Create Frame
            dialog.setBounds(300, 200, 600, 400);
            dialog.setVisible(true);
            
            JLabel jl2 = new JLabel("游戏结束！用时  "+format(elapsed)+"最高纪录为"+format(timeRecord)+"  点击确定继续"); // create a label
            
           
            
            final JButton but=new JButton("确定");
            dialog.setModal(true);
            dialog.setLayout(new FlowLayout());
            dialog.add(jl2);
            dialog.add(but);
          
            but.addActionListener(new ActionListener() {
                @Override
                public void actionPerformed(ActionEvent e) {
                	dialog.dispose();
                }});
         
            mm++;
        }
    	}
    }

   
	private static boolean end() {
        int empty = 0;
        for (int z = 0; z < 9; z++) {
            for (int x = 0; x < 3; x++) {
                for (int y = 0; y < 3; y++) {
                   
                    if (txtGame[z][x][y].getText().isEmpty())
                        empty++;
                }
            }
        }
        return empty != 0;
    }

    public static String[][] readfromFile(String filename) throws FileNotFoundException {
        String[][] s = new String[9][9];
        File inputFile = new File(filename);
        Scanner input = new Scanner(inputFile);
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                s[i][j] = input.next();
            }
        }
        return s;
        
    }



	private void addJRadioButton(final String text, int i) {
		JRadioButton radioButton = new JRadioButton(text);
		group.add(radioButton);
		pnlGame[i].add(radioButton);
		radioButton.addActionListener(new ActionListener() {

			@Override
			public void actionPerformed(ActionEvent e) {
				// TODO Auto-generated method stub

				if ("易".equals(text))
					count = 0;
				else if ("中".equals(text))
					count = 10;
				else if ("难".equals(text))
					count = 20;
				else
					count = 0;

			}

		});

	}

	public static void SaveIntoFile(JTextField[][][] tf) throws FileNotFoundException {
	String[][] s = new String[9][9];
	
	for (int z = 0; z < 9; z++) {
		for (int x = 0; x < 3; x++) {
			for (int y = 0; y < 3; y++) {
				if (!tf[z][x][y].getText().isEmpty())
					s[Cell.getX(z, x)][Cell.getY(z, y)] = tf[z][x][y].getText();
				else
					s[Cell.getX(z, x)][Cell.getY(z, y)] = "0";

			}
		}
	}
	PrintWriter output = new PrintWriter("save.txt");
	for(int i=0;i<9;i++) {
		for(int j=0;j<9;j++) {
	output.print(s[i][j]+"  ");
	}
		 output.println();}
    output.close();
}
	
	
	private class CountingThread extends Thread {
		public boolean goOn = true;
	    private CountingThread() {
	      setDaemon(true);
	    }
	    @Override
	    public void run() {
	      while (true) {
	        if (goOn) {
	          elapsed = System.currentTimeMillis() - programStart;
	          time.setText(format(elapsed));
	        }
	        try {
	          sleep(1); // 1毫秒更新一次显示
	        } catch (InterruptedException e) {
	          e.printStackTrace();
	          System.exit(1);
	        }
	      }
        
	    }
	    // 将毫秒数变为时秒分
	    
	  }
	public static String format(long elapsed) {
	      int hour, minute, second, milli;
	      milli = (int) (elapsed % 1000);
	      elapsed = elapsed / 1000;
	      second = (int) (elapsed % 60);
	      elapsed = elapsed / 60;
	      minute = (int) (elapsed % 60);
	      elapsed = elapsed / 60;
	      hour = (int) (elapsed % 60);
	      return String.format("%02d:%02d:%02d %03d", hour, minute, second, milli);
	    }
	 private static void compareTime(long t) throws FileNotFoundException {
			// TODO Auto-generated method stub
		 write(t);
		 File inputFile = new File("record.txt");
	     Scanner input = new Scanner(inputFile);
	     timeRecord=input.nextLong();
	     long l=-1;
	     while(input.hasNextLong()) {
	    	  l=input.nextLong();
	    	 if(l<timeRecord)
	    		 timeRecord=l;
	     
		}
	 }
	 private static void write(long t) {
		 FileOutputStream o = null;

		    
		    byte[] buff = new byte[]{};

		    try{

		        File file = new File("record.txt");

		        if(!file.exists()){

		            file.createNewFile();

		        }

		        buff=String.valueOf(t).getBytes();

		        o=new FileOutputStream(file,true);

		        o.write(buff);
		        byte[] bu = new byte[]{' '};
             o.write(bu);
		        o.flush();

		        o.close();

		    }catch(Exception e){

		        e.printStackTrace();

		    }
	 }
}
