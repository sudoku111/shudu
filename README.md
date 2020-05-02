 if(true) {
				 JFrame end=new JFrame("结束"); //Create Frame
				 end.setBounds(300, 200, 200, 100);
				 end.setVisible(true);
				 end.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
				 JLabel jl=new JLabel("You Win");  //create a label
				  Container c=end.getContentPane(); //get the window
				  c.add(jl); //add label to window
				  c.setVisible(true);
			 }
