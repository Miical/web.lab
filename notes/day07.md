## Workshop

### Starter

```js
router.post("/initsocket", (req, res) => {
  // do nothing if user not logged in
  // TODO (step 1.1): addUser when init socket
  if (req.user) {
    socketManager.addUser(req.user, socketManager.getSocketFromSocketID(req.body.socketid));
  }
  res.send({});
});
```

```js
  const handleLogin = (res) => {
    const userToken = res.tokenObj.id_token;
    post("/api/login", { token: userToken }).then((user) => {
      setUserId(user._id);
      // TODO (step 1.2): make post call to /api/initsocket
      post("/api/initsocket", { socketid: socket.id }) 
    });
  };

```

### Step 1

```js
const SingleUser = (props) => {
  return (
    <div
      className={`SingleUser-container u-pointer ${props.active ?
        "SingleUser-container--active" : ""
        }`}
      onClick={() => {
        props.setActiveUser(props.user);
      }}
    >
      {props.user.name}
    </div>
  );
}
```

```js
const ChatList = (props) => {
  return (
    <>
      <h3>Open Chats</h3>
      {props.users
        .map((user, i) => (
          <SingleUser
            key={i}
            setActiveUser={props.setActiveUser}
            user={user}
            active={user === props.active}
          />
        ))}
    </>
  );
}
```



## Step3: Add ChatList component to Chatbook

```js
 return (
    <>
      <div className="u-flex u-relative Chatbook-container">
        <div className="Chatbook-userList">
          <ChatList
            setActiveUser={setActiveUser}
            userId={props.userId}
            users={activeUsers}
            active={activeChat.recipient}
          />
        </div>
        <div className="Chatbook-chatContainer u-relative">
          <Chat data={activeChat} />
        </div>
      </div>
    </>
  );
```

### Step 4

```js
router.get("/activeUsers", (req, res) => {
  res.send({ activeUsers: socket.getAllConnectedUsers() });
```

### Step 5

```js
  useEffect(() => {
    get("/api/activeUsers").then((data) => {
      // If user is logged in, we load their chats. If they are not logged in,
      // there's nothing to load. (Also prevents data races with socket event)
      if (props.userId) {
        setActiveUsers([ALL_CHAT].concat(data.activeUsers));
      };
    });
  }, []);
```

### Step 7

```js
  const setActiveUser = (user) => {
    if (user._id !== activeChat.recipient._id) {
      setActiveChat({
        recipient: user,
        messages: [],
      });
    }
  };
```

### Step 8

```js
router.get("/chat", (req, res) => {
  let query;
  if (req.query.recipient_id === "ALL_CHAT") {
    // get any message sent by anybody to ALL_CHAT
    query = { "recipient._id": "ALL_CHAT" };
  } else {
    // get messages that are from me->you OR you->me
    query = {
      $or: [
        { "sender._id": req.user._id, "recipient._id": req.query.recipient_id },
        { "sender._id": req.query.recipient_id, "recipient._id": req.user._id },
      ],
    };
  }
```

```js
router.post("/message", auth.ensureLoggedIn, (req, res) => {
  console.log(`Received a chat message from ${req.user.name}: ${req.body.content}`);

  // insert this message into the database
  const message = new Message({
    recipient: req.body.recipient,
    sender: {
      _id: req.user._id,
      name: req.user.name,
    },
    content: req.body.content,
  });
  message.save();

  if (req.body.recipient._id == "ALL_CHAT") {
    socketManager.getIo().emit("message", message);
  } else {
    socketManager.getSocketFromUserID(req.user._id).emit("message", message);
    // TODO (step 10.1): implement the emit to the recipient of the message here.
    // It should look pretty similar to above! Except you want to emit to the
    // recipient instead of the currently logged in user
  }
});
```

