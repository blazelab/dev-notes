<http://stackoverflow.com/questions/35434832/how-to-do-waitall-with-akka-net>

```
public class TaskBasedUserLoader : ReceiveActor
{
    public TaskBasedUserLoader()
    {
        Receive<LoadUserRequest>(msg => LoadProfileAndMessages(msg));
    }

    private void LoadProfileAndMessages(LoadUserRequest msg)
    {
        var originalSender = Sender;
        var loadPreferences = this.LoadProfile(msg.UserId);
        var loadMessages = this.LoadMessages(msg.UserId);

        Task.WhenAll(loadPreferences, loadMessages)
            .ContinueWith(t => new UserLoadedResponse(loadPreferences.Result, loadMessages.Result), 
                TaskContinuationOptions.AttachedToParent & TaskContinuationOptions.ExecuteSynchronously)
            .PipeTo(originalSender);
    }

    private Task<UserProfile> LoadProfile(string userId)
    {
        return Task.FromResult(new UserProfile { UserId = userId });
    }

    private Task<List<Message>> LoadMessages(string userId)
    {
        return Task.FromResult(new List<Message>());
    }
}
```
