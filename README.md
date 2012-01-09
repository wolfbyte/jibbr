## JibbR

### A Jabbr bot designed for collaborative projects

#### another code52 project

Forking from Jabbot - a bot API for [JabbR](https://github.com/davidfowl/JabbR).

It's as easy as:

```csharp
var bot = new Bot("http://myjabbot", "username", "password");
bot.PowerUp();
bot.Join("someroom");
bot.Say("Hello", "someroom");
bot.Say("Ok I'm off");
bot.ShutDown();
```


## Writing Sprokets

Sprokets are things you can plug-in to enhance the behavior of your bot. Simply drop a dll with classes that implement
ISproket into a Sprokets folder and you're done. Here's an port of the [math.coffee](https://github.com/github/hubot/blob/master/src/scripts/math.coffee) from hubot:

```csharp
public class MathSproket : RegexSproket
{
    public override Regex Pattern
    {
        get { return new Regex("(calc|calculate|convert|math)( me)? (.*)"); }
    }

    protected override void ProcessMatch(Match match, ChatMessage message, Bot bot)
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.AcceptLanguage.Add(new StringWithQualityHeaderValue("en-us"));
        client.DefaultRequestHeaders.AcceptCharset.Add(new StringWithQualityHeaderValue("utf-8"));

        client.GetAsync("http://www.google.com/ig/calculator?hl=en&q=" + Uri.EscapeDataString(match.Groups[3].Value))
              .ContinueWith(task =>
              {
                  if (task.Result.IsSuccessStatusCode)
                  {
                      task.Result.Content.ReadAsStringAsync().ContinueWith(readTask =>
                      {
                          dynamic json =  JsonConvert.DeserializeObject(readTask.Result);

                          string solution = json.rhs;

                          bot.Reply(message.FromUser, solution ?? "Could not compute.", message.Room);
                      });
                  }
              });
    }
}
```

code52.org
=======
