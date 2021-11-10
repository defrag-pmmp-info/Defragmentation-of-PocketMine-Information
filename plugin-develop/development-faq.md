# PocketMineプラグイン開発FAQ
このページはプラグイン開発フォーラムでよく聞かれる質問について伝えることを目的にしています。コンテンツの大半はフォーラム上の質の良い投稿からまとめられたもので、参考があればリンクしてあります。

この元ページにはbitlink <https://bit.ly/PmDev> でアクセスしてください

## 翻訳について(dpmiより)
このページのコンテンツは[PocketMine Plugin Development FAQ](https://bit.ly/PmDev)から翻訳された内容です。元のコンテンツがunlicenseでだったことから、このページの内容もunlicenseとします。

# PHP
## Syntax error 構文エラー
これらはPHP自体での問題です。
These are pure PHP problems. [このStackOverflowのページ](http://stackoverflow.com/q/18050071/3990767)を見てください。細かいところまでが書かれています。

## ある数字が数字のリストの一つに等しいかを確認する
日本語で「そのIDは1,2,3,4のいずれかなら」というのは意味が通る内容ですが、PHPで`if($id == [1, 2, 3, 4])`や`if($id == 1 or 2 or 3 or 4)`などのように書くのは間違いです。
詳細は[このStackOverflowの質問](https://stackoverflow.com/q/21343646/3990767)と、さらに多くの方法を知りたければ[これ](https://stackoverflow.com/q/5593512/3990767)を見てください。

## ClassNotFoundException: Class `プラグイン内のクラス` not found
クラスのファイルがPSR-0に基づいておかれているかを確認してください。([ここでチェック](https://sof3.github.io/psr.htm)).

# Command API
## How to override a command?
As of API 3.0.0-ALPHA4 and before, you have to first rename the original command into something else using `Command::setLabel`, then remove it from the command map using `Command::unregister`. Then your own command class can be registered to the command map directly. <sup>_ref_ from [@iksaku](http://forums.pocketmine.net/thre7ads/overriding-default-7commands.8216/#post-87410) and [@shoghicp](https://gist.github.com/a540360b7323f7cc656f)</sup>

For example, to override the /help command:

```php
$map = $server->getCommandMap();
$old = $map->getCommand("help");
$old->setLabel("help_disabled");
$old->unregister($map);

$new = new MyHelpCommand($this);
$map->register($this->getName(), $new);
$map->register($this->getName(), $new, "?");
```

## Custom command usage / autocompletion
In API 2.0.0 to API 3.0.0-ALPHA4, you have to do this for automatic command completion:
The actual command completion part can be done using the "enum_values" option.

```php
// Class that extends pocketmine\command\Command

public function generateCustomCommandData(Player $player) {
   $commandData = parent::generateCustomCommandData($player);
   $commandData["overloads"]["default"]["input"]["parameters"] = [
      0 => [
         "type" => "stringenum",
         "name" => "parameter",
         "optional" => false,
         "enum_values" => ["parameter1", "parameter2"]
      ],
      1 => [
         "type" => "rawtext",
         "name" => "value",
         "optional" => true
      ],
      2 => [
         "type" => "string",
         "name" => "name",
         "optional" => true
      ]
   ];

   return $commandData;
}
```
For more information, refer to https://gist.github.com/NiclasOlofsson/db712fd9e3c9cb0777cd9381cb48915a <sup>[_ref_](https://forums.pmmp.io/threads/available-subcommand-parameters-above-command.1841/)</sup>

<!-- 編集者向け: https://github.com/pmmp/PocketMine-MP/blob/547503e8f44d1475def28bda2c0ed068eb24e670/changelogs/3.0-alpha.md#api-11 参照のとおり、この項目は不要となったため削除しました
# Event API
## \*\*\*Event does not have a handler list
You registered an event handler for an event that does not have a handler list. To fix this issue, find the parent class of the event that has a handler list, and handle that event class instead. Use `instanceof` in your event handler to filter events.

Take `EntityDamageByChildEntityEvent` as an example. Look at the [class file](https://github.com/pmmp/PocketMine-MP/blob/80292c6c7a542706a85c4f756e3d93bee1a07ca2/src/pocketmine/event/entity/EntityDamageByChildEntityEvent.php) &mdash; there is no `public static $handlerList;`, so you cannot make event handlers for it. Instead, look at the [class declaration](https://github.com/pmmp/PocketMine-MP/blob/80292c6c7a542706a85c4f756e3d93bee1a07ca2/src/pocketmine/event/entity/EntityDamageByChildEntityEvent.php#L29) to find out its superclass (`EntityDamageByEntityEvent`), and then [the superclass of the superclass](https://github.com/pmmp/PocketMine-MP/blob/80292c6c7a542706a85c4f756e3d93bee1a07ca2/src/pocketmine/event/entity/EntityDamageByEntityEvent.php#L30), vice versa, until you find a superclass [with the line `public static $handlerList = null;`](https://github.com/pmmp/PocketMine-MP/blob/80292c6c7a542706a85c4f756e3d93bee1a07ca2/src/pocketmine/event/entity/EntityDamageEvent.php#L32). Make your event handler handle this event class, and check if you are receiving `EntityDamageByChildEntityEvent` in the handler:

```php
public function handleDamage(EntityDamageEvent $event){
    if($event instanceof EntityDamageByChildEntityEvent){
        // handle $event here
    }
}
```
-->

# Scheduler API and threading
## How to cancel a task?
Use the [`ServerScheduler::cancelTask()`](https://github.com/pmmp/PocketMine-MP/blob/80292c6c7a542706a85c4f756e3d93bee1a07ca2/src/pocketmine/scheduler/ServerScheduler.php#L223) method. Do **not** use `Task::cancel()` &mdash; it only triggers internal callbacks of the task, but does not remove it from the scheduler.

Regarding the task ID, which is required when using `ServerScheduler::cancelTask()`. This can be obtained by `Task::getTaskId()`. If you cancel the task from itself, you can use `$serverScheduler->cancelTask($this->getTaskId())`. Otherwise, you have to retain a reference to the Task instance, or retrieve the task ID when scheduling the task. <sup>[_ref_](https://forums.pmmp.io/threads/creating-a-timer-with-tasks.136/)</sup>

For example:
```php
class MyTask extends PluginTask{
    public function onRun(int $ticks){
        if(needToCancelTask()){
            $this->getOwner()->getServer()->getScheduler()->cancelTask($this->getTaskId());
        }
    }
}
```

Or, store the task ID when scheduling the task:
```php
$this->id = $server->getScheduler()->scheduleRepeatingTask($myTask)->getTaskId();
// some time later...
$server->getScheduler()->cancelTask($this->id);
```

## How to cancel an event a few seconds later?
You don't do it with an event. You do it by comparing the system time.

This post is not yet cleaned up:

> ~~Timer~~ Scheduler & Tasks are for doing an action after a period of time, not having a state within a period of time. (The player will be unfrozen 10 seconds later vs The player cannot move for 10 seconds)

> What is the difference? For the former, "be unfrozen" is the action, while for the latter, "be immobile" is the state.

> How is a state different from an action? For an action, you do something actively. For example, "kill the player" is an action, but "the player is dead" is a state, a description, a condition.

> In programming, the things you put in a function are always actions. You change a value, you call a function, you stop the code from executing. How does a condition affect execution? A condition is queried upon when something is executing, and it does not actively executes. Your condition is "cannot move". What executions will query this condition? Of course, you only need to know whether the player can move when the player tries to move. This is, as you said, cancel an event. You handle a PlayerMoveEvent and cancel it if the condition applies.

> To be most convenient, you can change a value to make a player frozen/unfrozen, but this does not exist in PocketMine (there is Player->blocked, but it has side effects and it isn't very reliable). Therefore, you have to make PocketMine ask you whether the player can move every time the player tries to move. How do you yourself know whether the player can move? You ask yourself whether it is within the 10 seconds you asked for.

> But the time that code executes changes - how do you know if it is that relative 10 seconds? Therefore, we make the relative time absolute. For example, if I tell you that I typed this sentence in the past minute, you don't know when I typed this sentence. So how do you find it out? You look at the time this post is posted, and minus one miniute, to know what I meant by "in the past minute". Without the post timestamp from the forums? I have to tell you the exact (absolute) date and time, that is, 2016-11-16 17:38:05 UTC+8.

> Same goes to your code. You write down that "It is now 2016-11-16 17:38:05 UTC+8, and for the next 10 seconds the player can't move". Since time only goes forwards, I can make it even more straightforward: "The player can't move before 2016-11-16 17:38:15 UTC+8".

> An absolute time can be retrieved using the time() or microtime() function. So, you only store the timestamp for the "unfreeze" time, then in the PlayerMoveEvent handler, you check if the current time is less than the "unfreeze" time. You don't need to check if it is less than the "freeze" time, because time only goes forwards.

> You don't need to use scheduler tasks at all.

<sup>[_ref_](https://forums.pmmp.io/threads/learning-timer-or-task.57/)</sup>

## What is different between `scheduleDelayedTask`, `scheduleRepeatingTask`, `scheduleDelayedRepeatingTask` and `scheduleAsyncTask`?
Do not mix them up. There is something called `Task`, where `PluginTask` is a type of `Task`; and there is something called `AsyncTask`, which is entirely irrelevant with `Task`.

A `Task` can be scheduled to run after some time, which is `scheduleDelayedTask`; or to run repeatedly at given intervals, starting immediately, which is `scheduleRepeatingTask`; or to start running repeatedly after some time, which is `scheduleDelayedRepeatingTask`. The code in `Task` is executed on the main thread.

## What is threading? Does it make the server faster?
Threading is like executing code in another machine. Executing code in threads generally won't slow down the server unless you try to mine bitcoins. Each thread executes differently. We call the thread that the server does most of the work the "main thread". If the "main thread" is blocked (stops executing because it has to do something busy), it cannot respond to players, so it looks like the server hangs, or if it is only blocked for short periods, the server is laggy (decrease in TPS). **In simple words, threading is executing in background.**

An `AsyncTask` is executed in other threads, but an `AsyncTask` is not an independent thread. PocketMine internally has some threads called `AsyncWorker`. Each `AsyncWorker` is an independent thread, which you can think as another CPU. When you `scheduleAsyncTask`, PocketMine adds your new `AsyncTask` to a queue of AsyncTasks called the `AsyncPool`. An `AsyncWorker` will take the oldest `AsyncTask` from the `AsyncPool` and execute it, and then takes and execute the next one from the `AsyncPool`. (Imagine there are a few water pipes removing water from a water pool &mdash; the water at the bottom gets removed first) They are still executed on the same machine, possibly on another core (system-dependent, I guess). Using an AsyncTask will not give you infinite CPU, but may facilitate better utilization of all the CPU available.

You should only use `AsyncTask` to run one-time operations (e.g. usually takes less than 15 seconds to execute). If you are using it to run repeating stuff, such as starting another server (e.g. an HTTP server like Volt or an IRC client like the IRCClient plugin), start a thread instead. Otherwise, you will keep one of the workers busy (the water pipe is stuck), and the server will get one less AsyncWorker available. <sup>[_ref_](http://forums.pocketmine.net/threads/how-asynctask-reduces-lag.14056/#post-138729)</sup>

### AsyncTask: An analogy of a restaurant
In this analogy, a customer = a player, the waiter = main thread, food = some data from the Internet that takes a long time to download, the cook = the code that downloads data

1. A customer enters a restaurant and orders some food.
  * A player logins and asks the server to download some data from the Internet.
2. The waiter takes the order.
  * The server receives the command to download data.
3. The waiter writes a note to the cook to prepare the food.
  * The server starts an AsyncTask to download the data. While downloading data, the thread can't do anything else.
4. The waiter continues to serve other customers.
  * The server still serves the players, keeping them online, maintaining game mechanics, keep the server ticking, etc.
5. If the waiter does not ask the cook to prepare the food, but does the cooking himself, (assuming there is only one waiter) the customers will be left unattended, and they will be angry and leave the restaurant.
  * If the server downloads data on the main thread instead of in an AsyncTask, the players cannot see any response from the server to what they do, and may eventually timeout.
6. The waiter occasionally looks if the cook has finished preparing the food. (Looking at the table does not really stop him from serving customers, because it's just a glimpse and takes very little time)
  * The server checks if an AsyncTask is completed every tick.
7. The restaurant has a partition between the kitchen and the dining area, so the waiter and the cook cannot really talk directly.
  * Threads in PHP cannot communicate easily. They are often based on message stacks that may only be queried in long intervals.
8. The cook wants to say "the beef is ready, now preparing the noodles". He writes it on a note, and the waiter reads the note to the customer.
  * The AsyncTask announces that "50% downloaded" (using `AsyncTask::publishProgress`). The main thread checks for the progress every tick, and accordingly sends them to the player.
  * The main thread code is implemented in `AsyncTask::onProgressUpdate`, but they are actually run on the main thread.
9. If the food is ready, it is served to the player.
  * If the AsyncTask has a result, i.e. the data are downloaded, the data will be processed and the server accordingly reacts to the player.
  * The main thread code is implemented in `AsyncTask::onCompletion`, but they are actually run on the main thread.

<sup>_ref_: an ancient post from @PEMapModder that can no longer be found. @SOF3 recited it.</sup>

## What can be done in AsyncTasks?
**You can't call API methods on a separate thread. This is the undeniable fact; this is by definition of "API methods of PocketMine" and "threads".** The main body (`onRun`) of an AsyncTask is run entirely on a worker thread, and it is **incorrect, if not impossible** to call API methods from `AsyncTask::onRun()`. In addition to system limitations, most API operations you want to run in background actually have a bottleneck in network I/O with players, which cannot be prevented by threading anyway.

You may _request a delayed call_ of API methods _on the main thread_ from an AsyncTask using `publishProgress()` + `onProgressUpdate()`, or using `onCompletion()`, but **you still cannot call API methods on other threads**, but just on the main thread. You can **pass scalar/serializable data** between threads, and you can _request something to be done upon an object on the main thread_ through a complicated series of scheduling, but you **cannot** do them **directly on other threads**. You may _break down objects and reconstruct them_ in another thread, but **they will not be the same object**, i.e. if you somehow pass the Player object to an AsyncTask (which is actually impossible because it has a Server reference), **it will be a clone**, and _API methods won't work as they usually do_.

In other threads you may, however, call _static API methods_, such as `Utils::getURL()`, or even construct classes in the PocketMine API; but this is very dangerous, because if you aren't 100% sure what you are doing, you will end up trying to modify some data ineffectively without errors. _PHP builtin functions are not API methods_, but they are **safe to use as long as they don't reference any storage in the main thread**. For example, [`fopen`](https://php.net/fopen) returns a resource, which is actually an integer pointing to a handle stored in the PHP process; this resource ID will mean something probably entirely different if passed to another thread. You can still use resources in other threads, but make sure you don't try to reference resources in the main thread.

Therefore, **trying to call an API method on other threads will fail silently**. It is not possible, because you aren't calling API methods from _the_ server at all; you are calling them probably on a clone of the server, or a clone of other objects, etc., which **does not affect anything on the main thread**. <sup>[_ref_](https://forums.pmmp.io/threads/creating-async-task-and-call-methods-from-api.1000/#post-12255)</sup>

There are two typical uses:

### CPU-consuming code
* No, this is not how you call setBlock() in a large loop in world edit. Remember that you cannot execute PocketMine core API stuff in AsyncTask or any other threads. (to be explained below)
* One example is when you want to, say, delete a very big directory (e.g. the .git/ directory of a Git repo with many commits and branches like PocketMine, or the world folder of a map with many generated chunks). That is not related to PocketMine core, but it is time-consuming to run ("lagging the server") (I would consider anything taking more than half a tick, i.e. 25 ms "lagging the server"). You wouldn't expect it to take too much time (like a whole hour) to run, and you may execute it quite frequently.
* For example, you may be copying/deleting a SkyWars map 5 times every 10 minutes). This is when you want to use AsyncTask. AsyncTask wouldn't magically use less CPU. But it executes in another thread, which is like in another process (or is it really in a subprocess/another process? I'm not sure about how pthreads works internally).
So instead of spending the server's core tick time to do stuff that you don't need to get done on the pulse, do it in another thread.
* E.g. you can teleport the player to that world after the copying AsyncTask has completed; you don't need to do that immediately, and maybe your copying task spends 100 milliseconds to run, the player doesn't notice it but your server will lag behind for 2 ticks if you had not used an AsyncTask).

### Waiting-blocking
* No, I didn't say waiting for a player to do something. You can simply use the event API or the scheduler API to do that instead of suspending an async worker.
* This refers to thread-blocking function calls, such as curl_exec() (hence `Utils::getURL()` and `Utils::postURL()`). HTTP requests (cURL execution) would lag your server, because it spends most of the time waiting for a response from another server.
* Another example is MySQL queries. You aren't doing anything constructive in the majority time of a query function call. You are simply waiting for MySQL server to read your query, execute it and return a result.
* Why spend all the main thread performance on waiting for a response?
* It would of course be most convenient if you can set your queries/requests to be non-blocking and use a scheduler task to synchronously check if the server has respond. But in cases where you can't, we are simply putting the query aside and get a worker (another thread) stuck instead of getting the main thread stuck. Do you want the waiters look sick or the cooks to look sick, when your clients can see your waiters but not your cooks?
* It doesn't save CPU. It just redistributes the CPU use.

<sup>[_ref_](http://forums.pocketmine.net/threads/how-asynctask-reduces-lag.14056/#post-138729)</sup>

## Stored local complex data did not remove them after completion
Do not pass anything to the AsyncTask (parent) constructor unless you are going to use them in onCompletion() or onProgressUpdate(). If you do so, you must call fetchLocal() in onCompletion() to remove the data.

AsyncTask is not a PluginTask. You do not need to pass arguments to it.

<sup>[_ref_](https://forums.pmmp.io/threads/async-task-spam.3163/)</sup>

# Data saving
## YAML vs SQL
JSON is a subset of YAML. Any valid JSON data can be parsed as YAML. So unless you want to store the data in a compact way (which is pointless for small files, since [the size of each file is always multiples of 4 KB](https://unix.stackexchange.com/q/62049/161897)), YAML seems to always be a better option compared to JSON, unless your data will be parsed by a program that only knows JSON but not YAML. (After all, JSON has a long history but YAML is kind of new, but most languages already have YAML libraries)

If you have may data for many small units, e.g. a few numbers per player, you won't want to store all the data in the same YAML file, because it's slow. You have to load all the data into memory whenever you do I/O, which may cause memory failure.

Therefore, to save data for each player, each account should have its own file. However, it creates a large number of files, which many users may dislike. Also, it is inconvenient to evaluate statistics based on all data, e.g. mean, sum, maximum, etc. To do so with YAML, you have to either:

* Load the data of all units every time. But if you have 100000 players registered, you are in trouble. You have to open a file stream for each unit, read the contents, store them in a list, and close stream. This will lead to very poor performance, especially on personal computers, whose harddisks don't expect you to read so many files at a time. Therefore, this is not a good idea.
* Store the values. If you want a sum, store the sum somewhere and update it every time a datum is added/removed/updated. If you want an average or standard deviation, store the count too and also update it every time data are changed. If you want top 10, a bit trouble. If you want the median, basically you must recalculate all the data every time, unless you otherwise have an index of all numbers somewhere, which isn't per-player storage anymore. <sup>[_ref_](http://forums.pocketmine.net/threads/yaml-vs-sql.15091/#post-147453)</sup>

Oh the other hand, SQL has these advantages:

* All the data are stored in the same file, but the data are accessed using [`fseek`](https://php.net/fseek), which prevents loading unnecessary data. SQL is optimized for various queries, and data are indexed upon request, so it is more efficient.
* Most SQL implementations (e.g. MySQL) have good concurrency support, so multiple servers can access data together without conflict, if you know how to use it. Nevertheless, user-side setup might be slightly inconvenient.

Of course, the best solution is to let the users pick what database to use and bear their own consequences! See [SimpleAuth](https://github.com/PocketMine/SimpleAuth) for how to support switching between multiple database types.

# General gameplay
## Coordinate system
The Minecraft coordinate system complies with a right-handed Cartesian coordinate system, i.e. `x cross y = z`, as in this figure:

[![See Wikipedia's article on Right-hand Rule](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d2/Right_hand_rule_cross_product.svg/440px-Right_hand_rule_cross_product.svg.png)](https://en.wikipedia.org/wiki/Right-hand_rule)

In the Minecraft system, Y (the index finger) points upwards (negative Y points downwards). X points eastwards (negative X goes westwards), and Z points southwards (negative Z goes northwards).

The sun and moon come out from the east since Pocket Edition 1.1 <sup>[_ref_](https://minecraft.gamepedia.com/Day-night_cycle#History)</sup>. Clouds float westward, too <sup>[_ref_](https://minecraft.gamepedia.com/Cloud#Behavior)</sup>.

Rotation is measured by yaw (a.k.a. azimuth) and pitch. The yaw is the clockwise rotation from the south, i.e.:

| Yaw (Degrees), mod 360 | Yaw (Radians), mod 2&pi; | Direction | One step from (x = 0, z = 0) in this direction |
| :---: | :---: | :---: | :---: |
| 0 | 0 | South | (0, 1) |
| 90 | 0.5&pi; | West | (-1, 0) |
| 180 | &pi; | North | (0, -1) |
| 270 | 1.5&pi; | East | (1, 0) |

Pitch is the downward rotation. Starting at 0 at the horizontal, it increases to 90&deg; (0.5&pi;) when facing the ground, and decreases to -90&deg; (-0.5&pi;) when facing the sky.

A few cautions:
* The yaw and pitch are represented in degrees in most places in PocketMine, including `Entity::$yaw` and `Entity::$pitch`.
* The yaw is not guaranteed to be within the range `[0, 360)`. Be sure to mod 360 before comparing it.
* PHP's behaviour with `$negative_number % $positive_number` or `fmod($negative_number, $positive_number)`: a negative number is returned. But the yaw can be negative. Be sure to conditionally add 360 to the yaw after `% 360` if it is negative.
* PHP's `%` operator casts floats to ints. This may affect your results slightly. Use `fmod` instead; but it might still produce negative results.

Here is an alternative to `%`/`fmod` that always produces zero/positive float results:

```php
function positive_fmod(float $a, float $b) : float{
    return $a - floor($a / $b) * $b;
}
```

### Conversion of yaw+pitch to a unit vector <sup>[_src_](https://github.com/pmmp/PocketMine-MP/blob/master/src/pocketmine/entity/Entity.php#L1063-L1070)</sup>
```php
$y = -sin(deg2rad($this->pitch));
$xz = cos(deg2rad($this->pitch));
$x = -$xz * sin(deg2rad($this->yaw));
$z = $xz * cos(deg2rad($this->yaw));
return (new Vector3($x $y, $z))->normalize(); // TODO: verify if normalize() call is needed
```

### Evaluate yaw and pitch looking from $pos1 to $pos2 <sup>[_ref_](https://forums.pmmp.io/threads/direction.2285/#post-23772)</sup>
```php
$diff = $pos2->subtract($pos1)->normalize();
$pitch = asin(-$diff->y);
$yaw = acos($diff->z / cos($pitch));
```

***
### Legacy
| symbol | meaning |
| :----: | :-----: |
| _ref_ | Content is copied from the linked forum thread |
| _src_ | Content is based on evidence from source code |

!!! info

    [![Unlicense](https://img.shields.io/badge/license-Unlicense-blue.svg?style=plastic)](https://raw.githubusercontent.com/SOF3/forums-common-sense/master/LICENSE) This wiki is community work.

    In jurisdictions that recognize copyright laws, the author or authors of this software dedicate any and all copyright interest in the software to the public domain. We make this dedication for the benefit of the public at large and to the detriment of our heirs and successors. We intend this dedication to be an overt act of relinquishment in perpetuity of all present and future rights to this software under copyright law.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

    For more information, please refer to <http://unlicense.org>