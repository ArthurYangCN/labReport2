# ChatServer.java:

```
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;
import java.net.URI;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.List;

class ChatHandler implements URLHandler {
    List<String> messages; //store message
    String path; // Path

    ChatHandler(String path) throws IOException {
        this.path = path;
        this.messages = Files.readAllLines(Paths.get(path));
    }

    public String handleRequest(URI url) throws IOException {
        String query = url.getQuery();
        if (url.getPath().equals("/add-message")) {
            String[] params = query.split("&");
            if (params.length == 2 && params[0].startsWith("s=") && params[1].startsWith("user=")) {
                String message = params[0].substring(2);
                String user = params[1].substring(5);
                String addChat = user + ": " + message;
                this.messages.add(addChat);
                this.log(addChat);
                return String.join("\n", messages) + "\n";
            } else {
                return "Invalid input\n";
            }
        } else {
            return "Invalid path\n";
        }
    }

    void log(String s) {
        try (FileWriter fw = new FileWriter("session.log", true);
             BufferedWriter bw = new BufferedWriter(fw);
             PrintWriter out = new PrintWriter(bw)) {
            out.println(s);
        } catch (IOException e) {
            System.out.println("Error logging: " + e.getMessage());
        }
    }
}

class ChatServer {
    public static void main(String[] args) throws IOException {
        if (args.length != 2) {
            System.out.println("Usage: java ChatServer <port number> <file path>");
            return;
        }

        int port = Integer.parseInt(args[0]);
        Server.start(port, new ChatHandler(args[1]));
    }
}

```

# First time using `/add-message?s=Hello&user=Shihua`:
![Image](First.png)
1.

The `handleRequest(URI url)` method are called.
The `log(String s)` method, which records the chat entries to the session.log file.

2.

`URI url`: The argument of handleRequest. It includes the path `/add-message` and the string `s=Hello&user=Shihua`.
`List<String> messages`: It is an empty list for now. After using `/add-message`, this field contains every chat messages stored from the file(`reporttwo.txt`).
`String path`: This field holds the path to the file(`reporttwo.txt`). This won't change after using methods.

3.

`messages` field will be changed and contain "[Shihua: Hello]".


# Second time using `/add-message?s=How are you&user=yang`:
![Image](2.png)
1.

The `handleRequest(URI url)` method are called again.
The `log(String s)` method, which records the chat entries to the session.log file.

2.

`URI url`: The argument of handleRequest. It now includes the path `/add-message` and the string `s=How are you&user=yang`.
`List<String> messages`: Now, it already contains `"Shihua: Hello"` from the previous method call.
`String path`: This field holds the path to the file(`reporttwo.txt`). This won't change after using methods.

3.

`messages` field will be changed and contain `["Shihua: Hello", "yang: How are you"]`.
