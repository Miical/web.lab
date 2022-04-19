## Catbook 4: Data Fetching and Routing Workshop

### App navigation

URL (address bar) -> Router (component)

```react
const App = () => {
  return (
    // <> is like a <div>, but won't show
    // up in the DOM tree
    <>
      <NavBar />
      <div className="App-container">
      <Router>
        <Profile path="/profile" />
        <Feed path="/" />
        <NotFound default />
      </Router>
        {/* TODO (step5): use Router to route between pages */}
      </div>
    </>
  );
};
```

```react
const NavBar = () => {
  return (
    <nav className="NavBar-container">
      <div className="NavBar-title u-inlineBlock">Catbook</div>
      <div className="Nav-linkContainer u-inlineBlock">
        <Link className="NavBar-link" to="/">Home</Link>
        <Link className="NavBar-link" to="/profile">Profile</Link>
      </div>
      {/* TODO (step5): implement links to pages */}
    </nav>
  );
};
```

### Comment

Reminder of high level overview:

- Get to obtain stories
- SingleComment component renders data

```react
const Card = (props) => {
  // TODO (step6): define state to hold comments (refer to Feed)
  const [comments, setComments] = useState([]);

  useEffect(() => {
    get("/api/comment", {parent: props._id}).then((commentObjs) => {
      setComments(commentObjs);
    });
    // TODO (step6): implement a GET call to retrieve comments,
    // and assign it to state
  }, []);

  return (
    <div>
      <SingleStory _id={props._id} creator_name={props.creator_name} content={props.content} />
      {JSON.stringify(comments)}
    </div>
  )

  // TODO (step6): render a SingleStory using props,
  // and render the comments from state (with JSON.stringify)
  // TODO (step7): map comments from state into SingleComment
  // components (refer to Feed)
  // TODO (step8): add in the NewComment component (refer to Feed)
  // TODO (step9): use CommentsBlock
};
```

### Step 7

Implement SingleComment.js

```react
const Card = (props) => {
  const [comments, setComments] = useState([]);

  useEffect(() => {
    get("/api/comment", { parent: props._id }).then((commentItems) => {
      setComments(commentItems);
    });
  }, []);

  let commentsList = null;
  const hasComment = comments.length !== 0;
  if (hasComment) {
    commentsList = comments.map((commentObj) => (
      <SingleComment _id = {commentObj._id} creator_name={commentObj.creator_name} content={commentObj.content} /> 
    ));
  } else {
    commentsList = <div>No comment!</div>;
  }

  return (
    <div className="Card-container">
        <SingleStory
          _id={props._id}
          creator_name={props.creator_name}
          content={props.content}
        />
        {commentsList}
    </div>
  );
  // TODO (step7): map comments from state into SingleComment
  // components (refer to Feed)
  // TODO (step8): add in the NewComment component (refer to Feed)
  // TODO (step9): use CommentsBlock
};
```

### Step 8

Implement NewComment in NewPostInput.js

```react
const NewComment = () => {
  const addComment = (value) => {
    // TODO (step8): implement addComment (refer to NewStory)
    const body = { parent: props.storyId, content: value };
    post("/api/comment", body);
  };
  return <NewPostInput defaultText="New Comment" onSubmit={addComment} />;
  // TODO (step8): implement render (refer to NewStory)
};
```

### Step 9

```react
const CommentsBlock = () => {
  // TODO (step9): implement render
  return (
    <div className="Card-commentSection"> 
      {props.comments.map((commentObj) => (
        <SingleComment
          _id={commentObj._id}
          creator_name={commentObj.creator_name}
          content={commentObj.content}
        />
      ))}
      <NewComment storyId={props._id} />
    </div>
  );
};
```

