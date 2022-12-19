# Network Framework
 Peer to Peer using Network Framework macOS

Reference: https://github.com/AlwaysRightInstitute/alwaysrightinstitute.github.io/blob/09babf72df834d30abd4a365fa116469a584aa36/_posts/2020-10-12-network-framework.md

```
import Network

func machintoshI() {
    // Connect to the Bonjour service on the other Mac
    let connection = NWConnection(to:
            .service(name: "My Bonjour Service",
                     type: "_mybonjourservice._tcp",
                     domain: "local", interface: nil),
                     using: .udp)
    connection.start(queue: .main)

    // Send data over the connection
    if let data = "Hello World!".data(using: .utf8) {
        // The string was successfully encoded as UTF-8 data
        connection.send(content: data, completion: .contentProcessed({ (error) in
            connection.cancel()
            if let error = error {
                print("Failed to send data: \(error)")
            } else {
                print("Data sent successfully")
            }
        }))
    } else {
        // The string could not be encoded as UTF-8 data, so handle the error here
    }
}

```
import Network

func machintoshII() {
    do {
        let listener = try NWListener(using: .udp)
        
        listener.service = .init(name: "My Bonjour Service", type: "_mybonjourservice._tcp", domain: "local")
        listener.start(queue: .main)

        listener.newConnectionHandler = { connection in
            
            func readData() {
                connection.receive(minimumIncompleteLength: 1,
                                   maximumLength: 1024)
                {
                    data, context, isComplete, error in
                    
                    guard error == nil, let data = data else {
                        return connection.cancel()
                    }
                    
                    print("Received:", data)
                    readData() // recurse
                }
            }
            
            connection.start(queue: .main)
            readData()
        }
    } catch {
        print(error)
    }
}
```
