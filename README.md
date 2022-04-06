# Java_Netoworking

<a href="https://www.javatpoint.com/socket-programming">Socket-Java</a>

ServerSocket

```
 ServerSocket serverSocket = new ServerSocket(80);

        Socket client = serverSocket.accept();
        //client.getInputStream(); //Client odesílá data
        //client.getOutputStream(); //Clientovi odpovídáme
        Scanner scanner = new Scanner(client.getInputStream());

        while (scanner.hasNextLine()) {
            System.out.println(scanner.nextLine());
        }
```
