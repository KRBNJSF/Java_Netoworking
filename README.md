# Java_Networking

<a href="https://www.javatpoint.com/socket-programming">Socket-Java</a>

client.getInputStream(); //Client odesílá data
client.getOutputStream(); //Clientovi odpovídámeŁ

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
OUTPUT: 
```
GET / HTTP/1.1
Host: localhost
Connection: keep-alive
sec-ch-ua: " Not A;Brand";v="99", "Chromium";v="99", "Google Chrome";v="99"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.84 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Accept-Language: cs-CZ,cs;q=0.9

```

Otevřeme stránku <a href="https://linux4space.org/">Mázlova stránka</a> a inspirujeme se Req a Req headery

Network, poté ```F5``` a uvidíme Requesty, Responses

![image](https://user-images.githubusercontent.com/90755554/161961243-2f137c45-4895-41f0-a76c-c02b65c061fb.png)

![image](https://user-images.githubusercontent.com/90755554/161962047-0f0dc334-f95f-4acd-981e-e9b4448c2963.png)



Response Builder:  
Vytvoříme třídu HttpResponseBuilder

```
public class HttpResponseBuilder {

    public static final String NEW_LINE = "\r\n";
    public static final String SPACE = " ";
    public static final String BODY_SPLITTER = NEW_LINE + NEW_LINE;

    public static final String HTTP_VERSION_1_1 = "HTTP/1.1";

    public static final int STATUS_CODE_200 = 200;
    public static final int STATUS_CODE_500 = 500;

    public static final String HEADER_CONTENT_TYPE = "Content-Type";
    public static final String MIME_TYPE_HTML = "text/html";

    private String httpVersion;
    private int statusCode;
    private Map<String, String> headerParam;

    private String body;

    public HttpResponseBuilder() {
        headerParam = new HashMap<>();
    }

    public void setHttpVersion(String httpVersion) {
        this.httpVersion = httpVersion;
    }

    public void setStatusCode(int statusCode) {
        this.statusCode = statusCode;
    }

    public void setHeaderParam(Map<String, String> headerParam) {
        this.headerParam = headerParam;
    }

    public void setBody(String body) {
        this.body = body;
    }

    public String getHttpVersion() {
        return httpVersion;
    }

    public int getStatusCode() {
        return statusCode;
    }

    public Map<String, String> getHeaderParam() {
        return headerParam;
    }

    public String getBody() {
        return body;
    }

    public String build() {
        StringBuilder httpResponseBuilder = new StringBuilder();
        httpResponseBuilder.append(HTTP_VERSION_1_1)
                .append(SPACE)
                .append(statusCode)
                .append(NEW_LINE);
        for (Map.Entry<String, String> entry : headerParam.entrySet()) {
            httpResponseBuilder.append(entry.getKey())
                    .append(": ")
                    .append(entry.getValue())
                    .append(NEW_LINE);
        }

        if (body != null || !body.isEmpty()) {
            httpResponseBuilder
                    .append(BODY_SPLITTER);
                    httpResponseBuilder.append(body);
        }

        return httpResponseBuilder.toString();
    }

}
```

V Application.java -> 

```
 HttpResponseBuilder httpResponseBuilder = new HttpResponseBuilder();
        httpResponseBuilder.setBody("<html></html>");
        httpResponseBuilder.setStatusCode(200);
        httpResponseBuilder.getHeaderParam()
                .put(HttpResponseBuilder.HEADER_CONTENT_TYPE, HttpResponseBuilder.MIME_TYPE_HTML);

        System.out.println(httpResponseBuilder.build());
```

OUTPUT:

```
HTTP/1.1 200
Content-Type: text/html
```

Web response
---

HttpResponseBuilder ->
```
public class HttpResponseBuilder {

    public static final String NEW_LINE = "\r\n";
    public static final String SPACE = " ";
    public static final String BODY_SPLITTER = NEW_LINE;

    public static final String HTTP_VERSION_1_1 = "HTTP/1.1";

    public static final int STATUS_CODE_200 = 200;
    public static final int STATUS_CODE_500 = 500;

    public static final String HEADER_CONTENT_TYPE = "Content-Type";
    public static final String HEADER_CONTENT_LENGTH = "Content_Length";
    public static final String MIME_TYPE_HTML = "text/html";

    private String httpVersion;
    private int statusCode;
    private Map<String, String> headerParam;

    private String body;

    public HttpResponseBuilder() {
        headerParam = new HashMap<>();
    }

    public void setHttpVersion(String httpVersion) {
        this.httpVersion = httpVersion;
    }

    public void setStatusCode(int statusCode) {
        this.statusCode = statusCode;
    }

    public void setHeaderParam(Map<String, String> headerParam) {
        this.headerParam = headerParam;
    }

    public void setBody(String body) {
        this.body = body;
    }

    public String getHttpVersion() {
        return httpVersion;
    }

    public int getStatusCode() {
        return statusCode;
    }

    public Map<String, String> getHeaderParam() {
        return headerParam;
    }

    public String getBody() {
        return body;
    }

    public String build() {
        StringBuilder httpResponseBuilder = new StringBuilder();
        httpResponseBuilder.append(HTTP_VERSION_1_1)
                .append(SPACE)
                .append(statusCode)
                .append(NEW_LINE);
        for (Map.Entry<String, String> entry : headerParam.entrySet()) {
            httpResponseBuilder.append(entry.getKey())
                    .append(": ")
                    .append(entry.getValue())
                    .append(NEW_LINE);
        }

        if (body != null && !body.isEmpty()) {
            httpResponseBuilder
                    .append(BODY_SPLITTER);
            httpResponseBuilder.append(body);
        }

        return httpResponseBuilder.toString();
    }

}
```

Application.java -> 

```
public class Application {

    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(80);

        Socket client = serverSocket.accept();
        Scanner scanner = new Scanner(client.getInputStream());

        /*while (scanner.hasNextLine()) {
            System.out.println(scanner.nextLine());
        }*/

        HttpResponseBuilder httpResponseBuilder = new HttpResponseBuilder();
        httpResponseBuilder.setBody("Test body");
        httpResponseBuilder.setStatusCode(200);
        httpResponseBuilder.getHeaderParam()
                .put(HttpResponseBuilder.HEADER_CONTENT_TYPE, httpResponseBuilder.MIME_TYPE_HTML);


        OutputStream clientOutput = client.getOutputStream();

        OutputStreamWriter outputStreamWriter = new OutputStreamWriter(clientOutput);
        String response = httpResponseBuilder.build();
        outputStreamWriter.write(response);
        outputStreamWriter.flush();

        System.out.println(response);

    }

}
```

