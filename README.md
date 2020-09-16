# GOAP目标导向型行动计划（简称GOAP）是一种能够轻松呈现给你的代理选择的AI系统，也是帮助你可以无需维持一个庞大且复杂的有限状态机而做出明智的决策的机器。

演示版本

在这一演示版本中存在4种角色类别，每种类别使用的工具会在使用后一段时间被弄坏：

矿工：在岩石上挖矿。需要一个工具的帮忙。

樵夫：砍树去创造原木。需要一个工具的帮忙。

伐木工：将树切割成可使用的木头。需要一个工具的帮忙。

铁匠：在铁工厂锻造工具。每个人都要使用他所创造的工具。

每种类别将能够自动识别（使用模板导向型行动计划）自己需要采取怎样的行动去实现目标。如果他们的工具坏掉了，他们将前往提供铁匠所创造的工具的供应处。

什么是GOAP？

目标导向型行动计划是代理的一种人工智能系统，让你能够计划一系列行动去满足一个特殊的模板。特殊的行动序列不仅依靠于目标，同时也依靠于世界和代理当前的状态。这意味着如果同意的模板是针对于不同的代理或世界状态，你变回获得一个完全不同的行动序列，浙江能够让AI变得更加东太后且显示。让我们着眼于一个例子，即上述演示中所看到的内容。

我们拥有一个代理，他是伐木工，将把原木砍成木柴。这时候斧头将带着MakeFirewood的目标，并具有ChopLog，GetAxe和CollectBranches的行动。

ChopLog行动将把一根原木变成木柴，但前提是伐木工人拥有一把斧头。GetAxe行动将提供给伐木工人一把斧头。最后，CollectBranches行动将创造出木柴，并且不需要斧头，但是木柴的质量将不是很高。

当我们提供给代理MakeFirewood目标时，我们将看到两种不同的行动序列：

需要木柴–>GetAxe–>ChopLog–创造出木柴

需要木柴–>CollectBranches–创造出木柴

如果代理可以获得斧头，他们便会通过砍伐原木将其变成木柴。但如果他们不能获得斧头，他们便可以选择去收集树枝。每种序列都将完成MakeFirewood这个目标。

GOAP能够基于可行的先决条件去选择最佳序列。如果手边没有斧头，那么伐木工便不得不去寻找树枝。而这么做需要花费较长时间并只能获得一些低质量的木柴，所以我们不希望这种情况一致持续下去。

GOAP是针对于谁

到目前为止你可能熟悉有限状态机（FSM），但如果你并不熟悉，那么着眼于这个教程。

关于你的一些FSM代理，你可能遇到过某种较为复杂的状态，你可能会不想要添加新行为，因为它们会在AI中引起过多副作用和缺口。

GOAP会将如下状态：

fsm_states(from gamedevelopment)
fsm_states(from gamedevelopment)

变成这样：

goap_states(from gamedevelopment)
goap_states(from gamedevelopment)

通过减弱每个行动，现在我们可以单独专注于每个行动。这会让代码趋于模块化，变得更容易进行测试与维持。如果你想要往另一个行动添加新内容，你便可以直接做，而无需改变任何其它行动。尝试着基于一个FSM去做到这些！

同样地，你可以在运行中添加或删除行动去改变一个代理的行为从而让他们变得更加东太后。拥有一个食人魔会突然开始咆哮？那就提供给它们一个全新的“咆哮攻击”行动，并在它们平静下来时将其删除。你需要做的只是添加行动到行动列表上：GOAP计划者将负责剩下的内容。

如果你发现你的代理拥有一个非常复杂的FSM，你就应该够尝试下GOAP。你的FSM变得过于复杂的一个标志便是当所有的状态拥有大量条件语句并会测试它们接下来将变成何种状态，同时添加新的状态，这一切都会让你深深叹息。

如果你拥有一个非常简单的代理，只是执行1,2种任务的话，那么GOAP可能有点太过棘手，FSM便足够了。然而，你还是可以着眼于这里的相关理念并看看它们是否足够简单并能够应用于你的代理中。

行动

行动是代理做的事。通常情况下它只是以动画和声音呈现出来，并且会改变一些状态（游戏邦注：例如添加木柴）。打开一扇门与捡起铅笔是完全不同的行动（和动画）。行动与行动间是相互独立的，并不需要去担心其它行动是什么。

为了帮助GOAP决定我们想要使用怎样的行动，每个行动都会被赋予一个成本。在较低成本的行动中将不能选择较高成本的行动。当我们将行动排列在一起时，我们将合计成本，然后选择带有最低成本的序列。

让我们为行动分配一些成本：

GetAxe的成本：2

ChopLog的成本：4

CollectBranches的成本：8

如果我们再次着眼于行动序列并合计总成本，我们将看到最便宜的序列是：

需要木柴–>GetAxe（2）–>ChopLog（4）=创造木柴（总共是6）

需要木柴–>CollectBranches（8）=创造木柴（总共是8）

获得一把斧头并砍断原木创造木柴是基于较低成本6，而收集树枝创造木柴则是基于较高的成本8。所以我们的代理选择获得一把斧头去砍断树木。

但是是否将永远运行着同样的序列？如果我们引进先决条件的话会怎样？

先决条件和效果

行动拥有先决条件和效果。先决条件是行动运行所必须的状态，而效果则是行动运行后所发生的状态改变。

例如，ChopLog行动要求代理获得手边的斧头。如果代理并未拥有斧头，它便需要找到另外一个能够满足先决条件的行动，从而让ChopLog能够运行起来。幸运的是，GetAxe行动能够做到这点—-这是行动的效果。

GOAP计划者

GOAP计划者是段着眼于行动的先决条件和效果的代码，它将创造能够满足目标的序列。代理将朝着该目标前进，并伴随着一个世界状态以及代理能够执行的一列行动。基于这一信息，GOAP计划者能够排列行动，明确哪些是可运行的哪些不能，然后决定哪个行动是最突出的。幸运的是，我已经为你写好了这一代码，你无需再为此多花时间了。

为了进行设置，让我们添加先决条件和效果到伐木工的行动中：

GetAxe的成本：2。先决条件：“存在斧头，”“并未拥有斧头。”效果：“拥有一把斧头。”

ChopLog的成本：4。先决条件：“拥有一把斧头。”效果：“创造木柴。”

CollectBranches的成本：8。先决条件：（没有）。效果：“创造木柴。”

现在GOAP计划者拥有排列行动序列所需的信息去创造木柴（我们的目标）。

我们开始基于当前世界的状态和代理的状态执行GOAP计划者。这一组合的世界状态是：

“并未拥有斧头”

“存在斧头”

“阳光很耀眼”

着眼于我们当前可行的行动，唯一与它们相关的状态是“并未拥有斧头”和“存在斧头”状态；另外一个可能是用于使用其它行动的其它代理身上。

现在，我们拥有当前的世界状态，行动（带有自己的先决条件和效果）以及目标。让我们进行计划吧！

GOAL: “make firewood”
Current State: “doesn’t have an axe”, “an axe is available”
Can action ChopLog run?
NO – requires precondition “has an axe”
Cannot use it now, try another action.
Can action GetAxe run?
YES, preconditions “an axe is available” and “doesn’t have an axe” are true.
PUSH action onto queue, update state with action’s effect
New State
“has an axe”
Remove state “an axe is available” because we just took one.
Can action ChopLog run?
YES, precondition “has an axe” is true
PUSH action onto queue, update state with action’s effect
New State
“has an axe”, “makes firewood”
We have reached our GOAL of  “makes firewood”
Action sequence: GetAxe -> ChopLog

计划者将贯穿其它行动而运行，当它发现目标的解决方法时也不会停下来。如果另外一个序列拥有较低的成本的话要怎么办？它将贯穿所有可能性去寻找最佳解决方法。

当它在计划时，它将创造一棵树。每当行动得到落实，它将离开可行行动的列表，如此我们将不具有紧紧相连的50个GetAxe行动。状态是随着行动的效果发生改变。

计划者所创造的树如下：

freemind(from gamedevelopment)
freemind(from gamedevelopment)

我们可以看到它会找到3种通向目标的方法：

GetAxe–>ChopLog（总成本是6）

GetAxe–>CollectBranches（总成本是10）

CollectBranches（总成本是8）

尽管GetAxe–>CollectBranches是可行的，但是最便宜的方法应该是GetAxe–>ChopLog。

先决条件和效果在代码中是怎样的？这是取决于你，但我发现将其作为键值对进行储存是最简单的，即在此键始终是一串字符，而值则是一个对象或基本类型（float，int，Boolean之类的）。在C#中是这样的：

HashSet< KeyValuePair<string,object> > preconditions;

HashSet< KeyValuePair<string,object> > effects;

当行动在执行时，这些效果是怎样的，它们将做什么？其实它们什么也不需要做—-它们只是用于计划，在真正运行前并不会对代理的状态产生影响。

这是值得强调的：计划行动与运行它们并不相同。当一个代理执行GetAxe行动时，可能在它周围就有许多工具，将播放弯腰捡东西的动画，然后再把斧头装进它的背包中。这改变了代理的状态。但是，在GOAP计划期间，状态改变只是暂时的，所以计划者可以明确最佳解决方法。

程序上的先决条件

有时候，行动需要做更多事去决定它们是否可以运行。例如，GetAxe行动拥有“存在斧头”这样的先决条件即需要去探索世界，以判断是否存在代理可以使用的斧头。它将决定最近的斧头是很遥远或者藏在敌人背后，并将传达它不能运行的信息。这一先决条件便是程序上的，并需要运行一些代码；这并不只是我们能够触发的简单的Boolean操作符。

显然，这些程序上的先决条件需要花些时间才能运行，并且将执行于渲染线程以外的内容，最理想的便是背景线程或作为Coroutines（在Unity中）。

你也将拥有程序效果，如果你希望的话。如果你想要引进更加动态化的结果模拟将能够在运行过程中改变行动成本！

GOAP和状态

我们的GOAP系统将需要呈现在一个小型的有限状态机中，因为在许多游戏中，行动需要为了执行而接近一个目标。我们最终拥有3种状态：

Idle

MoveTo

PerformAction

当出现idle时，代理将明确他们想要完成的目标。这一部分是在GOAP外部进行处理；GOAP将只是告诉你能够执行目标的行动。当选择好目标时，它将被转移到GOAP计划者中，并伴随着世界和代理的开始状态，计划者将传回一个行动列表（游戏邦注：如果它可以实现目标的话）。

当计划者完成任务而代理拥有一个行动列表时，它将尝试着执行第一个行动。所有的行动需要明确它们是否需要处在目标范围内。如果它们这么做，那么FSM将推动下一种状态：MoveTo。

MoveTo状态将告诉代理它需要移向特定的目标。代理将作出移动（播放行走的动画），然后让FSM知道它何时是处于目标的范围内。这一状态之后将被删除，从而行动将能够执行。

PerformAction状态将在GOAP计划者所传回的行动序列中运行下一个行动。行动可以是即时的或者持续好几帧，但当它完成后将离开列表，然后下一个行动便可以开始执行（再一次地，需要在一个对象范围内检查下一个行动是否需要被执行）。

这些内容将一直重复着直到不再有行动可以执行，那时候我们将回到Idle状态，获得一个新目标并再次进行计划。

真正的代码例子

是时候着眼于真正的例子了！别担心；这并不复杂，我提供了Unity和C#中的一份工作副本能够帮助你进行尝试。我将在此简单地进行阐述，从而让你可以进行探索。代码使用了之前提到的WoodChopper例子。

如果你想做的更好，先在此获得代码：http://github.com/sploreg/goap

我们拥有4种劳工：

铁匠：将铁矿石变成工具。

樵夫：使用一种工具将树砍成木头。

矿工：使用一种工具去挖矿石而创造铁矿石。

伐木工：使用一种工具将木头砍成木柴。

工具将随着时间的流逝而磨损，并需要被替换。幸运的是，铁匠能够创造工具。但他们需要铁矿石才能创造工具，这时候便需要矿工的帮忙（他同样也需要工具）。伐木工需要木头，而这些是源自樵夫；他们都需要工具。

工具和资源被储存在供应处。代理将从供应处收集他所需要的材料或工具，同时在这里放下他们所创造的产品。

代码具有6种主要的GOAP类别：

GoapAgent：了解状态并使用FSM和GoapPlanner去操作。

GoapAction：代理能够执行的行动。

GoapPlanner：计划GoapAgent的行动。

FSM：有限的状态机。

FSMState：FSM中的一种状态。

IGoap：我们的劳工演员所使用的界面。与GOAP和FSM的事件相关联。

让我们着眼于GoapAction类别，因为这是你需要划入子集的内容：

public abstract class GoapAction : MonoBehaviour {

private HashSet<KeyValuePair<string,object>> preconditions;
private HashSet<KeyValuePair<string,object>> effects;

private bool inRange = false;

/* The cost of performing the action.
* Figure out a weight that suits the action.
* Changing it will affect what actions are chosen during planning.*/
public float cost = 1f;

/**
* An action often has to perform on an object. This is that object. Can be null. */
public GameObject target;

public GoapAction() {
preconditions = new HashSet<KeyValuePair<string, object>> ();
effects = new HashSet<KeyValuePair<string, object>> ();
}

public void doReset() {
inRange = false;
target = null;
reset ();
}

/**
* Reset any variables that need to be reset before planning happens again.
*/
public abstract void reset();

/**
* Is the action done?
*/
public abstract bool isDone();

/**
* Procedurally check if this action can run. Not all actions
* will need this, but some might.
*/
public abstract bool checkProceduralPrecondition(GameObject agent);

/**
* Run the action.
* Returns True if the action performed successfully or false
* if something happened and it can no longer perform. In this case
* the action queue should clear out and the goal cannot be reached.
*/
public abstract bool perform(GameObject agent);

/**
* Does this action need to be within range of a target game object?
* If not then the moveTo state will not need to run for this action.
*/
public abstract bool requiresInRange ();

/**
* Are we in range of the target?
* The MoveTo state will set this and it gets reset each time this action is performed.
*/
public bool isInRange () {
return inRange;
}

public void setInRange(bool inRange) {
this.inRange = inRange;
}

public void addPrecondition(string key, object value) {
preconditions.Add (new KeyValuePair<string, object>(key, value) );
}

public void removePrecondition(string key) {
KeyValuePair<string, object> remove = default(KeyValuePair<string,object>);
foreach (KeyValuePair<string, object> kvp in preconditions) {
if (kvp.Key.Equals (key))
remove = kvp;
}
if ( !default(KeyValuePair<string,object>).Equals(remove) )
preconditions.Remove (remove);
}

public void addEffect(string key, object value) {
effects.Add (new KeyValuePair<string, object>(key, value) );
}

public void removeEffect(string key) {
KeyValuePair<string, object> remove = default(KeyValuePair<string,object>);
foreach (KeyValuePair<string, object> kvp in effects) {
if (kvp.Key.Equals (key))
remove = kvp;
}
if ( !default(KeyValuePair<string,object>).Equals(remove) )
effects.Remove (remove);
}

public HashSet<KeyValuePair<string, object>> Preconditions {
get {
return preconditions;
}
}

public HashSet<KeyValuePair<string, object>> Effects {
get {
return effects;
}
}
}

这里没什么特别的内容：它储存了先决条件和效果。它同样也知道它是否必须处于目标范围内，如果这样的话，FSM便知道在需要的时候推动MoveTo状态。它也知道它是何时完成的；这是由执行行动类别所决定。

以下是其中的一种行动：

public class MineOreAction : GoapAction
{
private bool mined = false;
private IronRockComponent targetRock; // where we get the ore from

private float startTime = 0;
public float miningDuration = 2; // seconds

public MineOreAction () {
addPrecondition (“hasTool”, true); // we need a tool to do this
addPrecondition (“hasOre”, false); // if we have ore we don’t want more
addEffect (“hasOre”, true);
}

public override void reset ()
{
mined = false;
targetRock = null;
startTime = 0;
}

public override bool isDone ()
{
return mined;
}

public override bool requiresInRange ()
{
return true; // yes we need to be near a rock
}

public override bool checkProceduralPrecondition (GameObject agent)
{
// find the nearest rock that we can mine
IronRockComponent[] rocks = FindObjectsOfType ( typeof(IronRockComponent) ) as IronRockComponent[];
IronRockComponent closest = null;
float closestDist = 0;

foreach (IronRockComponent rock in rocks) {
if (closest == null) {
// first one, so choose it for now
closest = rock;
closestDist = (rock.gameObject.transform.position – agent.transform.position).magnitude;
} else {
// is this one closer than the last?
float dist = (rock.gameObject.transform.position – agent.transform.position).magnitude;
if (dist < closestDist) {
// we found a closer one, use it
closest = rock;
closestDist = dist;
}
}
}
targetRock = closest;
target = targetRock.gameObject;

return closest != null;
}

public override bool perform (GameObject agent)
{
if (startTime == 0)
startTime = Time.time;

if (Time.time – startTime > miningDuration) {
// finished mining
BackpackComponent backpack = (BackpackComponent)agent.GetComponent(typeof(BackpackComponent));
backpack.numOre += 2;
mined = true;
ToolComponent tool = backpack.tool.GetComponent(typeof(ToolComponent)) as ToolComponent;
tool.use(0.5f);
if (tool.destroyed()) {
Destroy(backpack.tool);
backpack.tool = null;
}
}
return true;
}

}

最大的行动部分是checkProceduralPreconditions方法。它是伴随着IronRockComponent在寻找最近的游戏对象，并保存这一目标演示。然后当它开始执行的时候，它将保存目标岩石并基于它去执行行动。当行动被再次使用时，它的所有领域都将被重置，从而让它们能够再次得到计算。

这是在Unity中添加到矿工实体对象的所有组件：

components(from gamedevelopment)
components(from gamedevelopment)

为了让你的代理能够运行，你必须添加如下组件：

GoapAgent。

执行IGoap的一种类别（也就是上述例子中的Miner.cs）

一些行动。

一个背包（只是因为行动在使用它；这与GOAP不相关）。

你可以添加自己想要的任何行动，这将改变代理的行为。你甚至可以提供给它所有的行动，从而让它可以挖矿石，锻造工具并砍伐树木。

每个劳工将走向他们需要行动的目标（树，岩石，砧板等待）去执行行动。铁匠将保持等待直至供应处出现铁矿石（来自矿工）。然后铁匠将去创造工具，并将工具放到离自己最近的供应处。当劳工的工具破损时，他们将前往铁匠附近的供应处获取新工具。

你可以在此获取新代码：http://github.com/sploreg/goap

结论

基于GOAP，你可以无需烦恼伴随着有限状态机出现的连通状态而创造出许多行动系列。你可以从代理手上添加或删除行动去创造出动态结果，同时也能够让你理智地维持代码的运行。你最终将获得一个灵活，机智且动态的AI。

（本文为游戏邦/gamerboom.com编译，拒绝任何不保留版权的转载，如需转载请联系：游戏邦）

Goal Oriented Action Planning for a Smarter AI

by Brent Owens

Goal Oriented Action Planning (GOAP) is an AI system that will easily give your agents choices and the tools to make smart decisions without having to maintain a large and complex finite state machine.

View the Demo

In this demo, there are four character classes, each using tools that break after being used for a while:

Miner: Mines ore at rocks. Needs a tool to work.

Logger: Chops trees to produce logs. Needs a tool to work.

Wood Cutter: Cuts up trees into usable wood. Needs a tool to work.

Blacksmith: Forges tools at the forge. Everyone uses these tools.

Each class will figure out automatically, using goal oriented action planning, what actions they need to perform to reach their goals. If their tool breaks, they will go to a supply pile that has one made by the blacksmith.

What is GOAP?

Goal oriented action planning is an artificial intelligence system for agents that allows them to plan a sequence of actions to satisfy a particular goal. The particular sequence of actions depends not only on the goal but also on the current state of the world and the agent. This means that if the same goal is supplied for different agents or world states, you can get a completely different sequence of actions., which makes the AI more dynamic and realistic. Lets look at an example, as seen in the demo above.

We have an agent, a wood chopper, that takes logs and chops them up into firewood. The chopper can be supplied with the goal MakeFirewood, and has the actions ChopLog, GetAxe, and CollectBranches.

The ChopLog action will turn a log into firewood, but only if the wood cutter has an axe. The GetAxe action will give the wood cutter an axe. Finally, the CollectBranches action will produce firewood as well, without requiring an axe, but the firewood will not be as high in quality.

When we give the agent the MakeFirewood goal, we get these two different action sequences:

Needs firewood -> GetAxe -> ChopLog = makes firewood

Needs firewood -> CollectBranches = makes firewood

If the agent can get an axe, then they can chop a log to make firewood. But maybe they cannot get an axe; then, they can just go and collect branches. Each of these sequences will fulfill the goal of MakeFirewood.

GOAP can choose the best sequence based on what preconditions are available. If there is no axe handy, then the wood cutter has to resort to picking up branches. Picking up branches can take a really long time and yield poor quality firewood, so we don’t want it to run all the time, only when it has to.

Who GOAP is For

You are, by now, probably familiar with Finite State Machines (FSM), but if not, then take a look at this terrific tutorial.

You might have run into very large and complex states for some of your FSM agents, where you eventually get to a point where you do not want to add new behaviours because they cause too many side effects and gaps in the AI.

GOAP turns this:

into this:

Finite State Machine states: connected everywhere.

Into this:

GOAP: nice and manageable.

By decoupling the actions from each other, we can now focus on each action individually. This makes the code modular, and easy to test and to maintain. If you want to add in another action, you can just plunk it in, and no other actions have to be changed. Try doing that with an FSM!

Also, you can add or remove actions on the fly to change the behaviour of an agent to make them even more dynamic. Have an ogre that suddenly started raging? Give them a new “rage attack” action that gets removed when they calm down. Simply adding the action to the list of actions is all you have to do; the GOAP planner will take care of the rest.

If you find you have a very complex FSM for your agents, then you should give GOAP a try. One sign your FSM is getting too complex is when every state has a myriad of if-else statements testing what state they should go to next, and adding in a new state makes you groan at all the implications it might have.

If you have a very simple agent that just performs one or two tasks, then GOAP might be a little heavy-handed and an FSM will suffice. However, it is worth looking at the concepts here and seeing whether they would be easy enough for you to plug into your agent.

Actions

An action is something that the agent does. Usually it is just playing an animation and a sound, and changing a little bit of state (for instance, adding firewood). Opening a door is a different action (and animation) than picking up a pencil. An action is encapsulated, and should not have to worry about what the other actions are.

To help GOAP determine what actions we want to use, each action is given a cost. A high cost action will not be chosen over a lower cost action. When we sequence the actions together, we add up the costs and then choose the sequence with the lowest cost.

Lets assign some costs to the actions:

GetAxe Cost: 2

ChopLog Cost: 4

CollectBranches Cost: 8

If we look at the sequence of actions again and add up the total costs, we will see what the cheapest sequence is:

Needs firewood -> GetAxe (2) -> ChopLog (4) = makes firewood (total: 6)

Needs firewood -> CollectBranches (8) = makes firewood (total: 8)

Getting an axe and chopping a log produces firewood at the lower cost of 6, while collecting the branches produces wood at the higher cost of 8. So, our agent chooses to get an axe and chop wood.

But won’t this same sequence run all the time? Not if we introduce preconditions…

Preconditions and Effects

Actions have preconditions and effects. A precondition is the state that is required for the action to run, and the effects are the change to the state after the action has run.

For example, the ChopLog action requires the agent to have an axe handy. If the agent does not have an axe, it needs to find another action that can fulfill that precondition in order to let the ChopLog action run. Luckily, the GetAxe action does that—this is the effect of the action.

The GOAP Planner

The GOAP planner is a piece of code that looks at actions’ preconditions and effects, and creates queues of actions that will fulfill a goal. That goal is supplied by the agent, along with a world state, and a list of actions the agent can perform. With this information the GOAP planner can order the actions, see which can run and which can’t, and then decide which actions are the best to perform. Luckily for you, I’ve written this code, so you don’t have to.

To set this up, lets add preconditions and effects to our wood chopper’s actions:

GetAxe Cost: 2. Preconditions: “an axe is available”, “doesn’t have an axe”. Effect: “has an axe”.

ChopLog Cost: 4. Preconditions: “has an axe”. Effect: “make firewood”

CollectBranches Cost: 8. Preconditions: (none). Effect: “make firewood”.

The GOAP planner now has the information needed to order the sequence of actions to make firewood (our goal).

We start by supplying the GOAP Planner with the current state of the world and the state of the agent. This combined world state is:

“doesn’t have an axe”

“an axe is available”

“the sun is shining”

Looking at our current available actions, the only part of the states that are relevant to them is the “doesn’t have an axe” and the “an axe is available” states; the other one might be used for other agents with other actions.

Okay, we have our current world state, our actions (with their preconditions and effects), and the goal. Let’s plan!

The planner will run through the other actions, too, and it won’t just stop when it finds a solution to the goal. What if another sequence has a lower cost? It will run through all possibilities to find the best solution.

When it plans, it builds up a tree. Every time an action is applied, it is popped off the list of available actions, so we don’t have a string of 50 GetAxe actions back-to-back. The state is changed with that action’s effect.

The tree that the planner builds up looks like this:

We can see that it will actually find three paths to the goal with their total costs:

GetAxe -> ChopLog (total: 6)

GetAxe -> CollectBranches (total: 10)

CollectBranches (total: 8)

Although GetAxe -> CollectBranches works, the cheapest path is GetAxe -> ChopLog, so this one is returned.

What do preconditions and effects actually look like in code? Well, that’s up to you, but I have found it easiest to store them as a key-value pair, where the key is always a String and the value is an object or primitive type (float, int, Boolean, or similar). In C#, that could look like this:

When the action is performing, what do these effects actually look like and what do they do? Well, they don’t have to do anything—they are really just used for planning, and don’t affect the real agent’s state until they run for real.

This is worth emphasising: planning actions is not the same as running them. When an agent performs the GetAxe action, it will probably be near a pile of tools, play a bend-down-and-pick-up animation, and then store an axe object in its backpack. This changes the state of the agent. But, during GOAP planning, the state change is just temporary, so that the planner can figure out the optimal solution.

Procedural Preconditions

Sometimes, actions need to do a little more to determine whether they can run. For instance, the GetAxe action has the precondition of “an axe is available” that will need to search the world, or the immediate vicinity, to see whether there is an axe the agent can take. It might determine that the nearest axe is just too far away or behind enemy lines, and will say that it cannot run. This precondition is procedural and needs to run some code; it’s not a simple Boolean operator that we can just toggle.

Obviously, some of these procedural preconditions can take a while to run, and should be performed on something other than the render thread, ideally as a background thread or as Coroutines (in Unity).

You could have procedural effects too, if you so desire. And if you want to introduce even more dynamic results, you can change the cost of actions on the fly!

GOAP and State

Our GOAP system will need to live in a small Finite State Machine (FSM), for the sole reason that, in many games, actions will need to be close to a target in order to perform. We end up with three states:

Idle

MoveTo

PerformAction

When idle, the agent will figure out which goal they want to fulfill. This part is handled outside of GOAP; GOAP will just tell you which actions you can run to perform that goal. When a goal is chosen it is passed to the GOAP Planner, along with the world and agent starting state, and the planner will return a list of actions (if it can fulfill that goal).

When the planner is done and the agent has its list of actions, it will try to perform the first action. All actions will need to know if they must be in range of a target. If they do, then the FSM will push on the next state: MoveTo.

The MoveTo state will tell the agent that it needs to move to a specific target. The agent will do the moving (and play the walk animation), and then let the FSM know when it is within range of the target. This state is then popped off, and the action can perform.

The PerformAction state will run the next action in the queue of actions returned by the GOAP Planner. The action can be instantaneous or last over many frames, but when it is done it gets popped off and then the next action is performed (again, after checking whether that next action needs to be performed within range of an object).

This all repeats until there are no actions left to perform, at which point we go back to the Idle state, get a new goal, and plan again.

A Real Code Example

It’s time to take a look at a real example! Don’t worry; it isn’t that complicated, and I have provided a working copy in Unity and C# for you to try out. I will just talk about it briefly here so you get a feel for the architecture. The code uses some of the same WoodChopper examples as above.

If you want to dig right in, head here for the code: http://github.com/sploreg/goap

We have four labourers:

Blacksmith: turns iron ore into tools.

Logger: uses a tool to chop down trees to produce logs.

Miner: mines rocks with a tool to produce iron ore.

Wood cutter: uses a tool to chop logs to produce firewood.

Tools wear out over time and will need to be replaced. Fortunately, the Blacksmith makes tools. But iron ore is needed to make tools; that’s where the Miner comes in (who also needs tools). The Wood Cutter needs logs, and those come from the Logger; both need tools as well.

Tools and resources are stored on supply piles. The agents will collect the materials or tools they need from the piles, and also drop off their product at them.

The code has six main GOAP classes:

GoapAgent: understands state and uses the FSM and GoapPlanner to operate.

GoapAction: actions that agents can perform.

GoapPlanner: plans the actions for the GoapAgent.

FSM: the finite state machine.

FSMState: a state in the FSM.

IGoap: the interface that our real Labourer actors use. Ties into events for GOAP and the FSM.

Lets look at the GoapAction class, since that is the one you will subclass:

Nothing too fancy here: it stores preconditions and effects. It also knows whether it must be in range of a target, and, if so, then the FSM knows to push the MoveTo state when needed. It knows when it is done, too; that is determined by the implementing action class.

Here is one of the actions:

The largest part of the action is the checkProceduralPreconditions method. It looks for the nearest game object with an IronRockComponent, and saves this target rock. Then, when it performs, it gets that saved target rock and will perform the action on it. When the action is re-used in planning again, all of its fields are reset so that they can be calculated again.

These are all components that are added to the Miner entity object in Unity:

In order for your agent to work, you must add the following components to it:

GoapAgent.

A class that implements IGoap (in the above example, that’s Miner.cs).

Some actions.

A backpack (only because the actions use it; it is unrelated to GOAP).

You can add whatever actions you want, and this would change how the agent behaves. You could even give it all actions so it can mine ore, forge tools, and chop wood.

Here is the demo in action again:

Each labourer goes to the target that they need to fulfill their action (tree, rock, chopping block, or whatever), performs the action, and often returns to the supply pile to drop off their goods. The Blacksmith will wait a little while until there is iron ore in one of the supply piles (added to by the Miner). The Blacksmith then goes off and makes tools, and will drop off the tools at the supply pile nearest him. When a labourer’s tool breaks they will head off to the supply pile near the Blacksmith where the new tools are.

You can grab the code and the full app here: http://github.com/sploreg/goap.

Conclusion

With GOAP, you can create a large series of actions without the headache of interconnected states that often comes with a Finite State Machine. Actions can be added and removed from an agent to produce dynamic results, as well as to keep you sane when maintaining the code. You will end up with a flexible, smart, and dynamic AI.(sourcr:gamedevelop.tutsplus)
