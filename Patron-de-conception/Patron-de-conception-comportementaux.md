# [Patron de conception comportementaux](../Design-Pattern.md)
* [Chaine of Responsability](#chaine-of-responsability)
* [Command](#command)
* [Iterator](#iterator)
* [Mediator](#mediator)
* [Memento](#memento)
* [Observer](#observer)
* [State](#state)
* [Strategy](#strategy)
* [Template Method](#template-method)
* [Visitor](#visitor)

# [Chaine of Responsability](#patron-de-conception-comportementaux)
Faire circuler des demandes dans une chaîne de handlers permet de déléguer successivement le traitement d'une requête à travers une séquence d'objets, chacun étant responsable d'une étape spécifique.
```csharp
class User {
    string Username { get; set; }
    string Password { get; set; }
    DateTime Expiration { get; set }
    string Role { get; set; }
}
record Connection(string Username, string Password) {}
class ErrorConnection : System.Exception {
    int Code { get; set; }
    string Message { get; set; }
}
interface ConnectionHandler<T> {
    void Connection(Connection connection, T information);
}
class UserInformationIsCorrect : ConnectionHandler<User> {
    void Connection(Connection connection, User user) {
        if (connection.Username != user.Username || connection.Password != user.Password)
            throw new ErrorConnection {
                Code = 400,
                Message = "Username or Passowrd is incorrect"
            };
    }
}
class UserExpirationIsCorrect : ConnectionHandler<Void> {
    void Connection(Connection connection) {
        if (connection.Expiration > DateTime.Now)
            throw new ErrorConnection {
                Code = 400,
                Message = "User is expired"
            };
    }
}
class UserRoleIsCorrect : ConnectionHandler<string> {
    void Connection(Connection connection, string role) {
        if (connection.role != role)
            throw new ErrorConnection {
                Code = 400,
                Message = $"The role {role} is attempting"
            };
    }
}
```
# [Command](#patron-de-conception-comportementaux)
Permet de paramétrer des méthodes avec différentes actions, planifier leur exécution, les mettre dans une file d’attente ou d’annuler des opérations effectuées.
````csharp
class Receiver {
    public void Action() =>
        Console.WriteLine("Receiver executing action.");
}
record Invoker(Button button) {
    public void ExecuteCommand() =>
        button.Execute();
}
class ValideButton : Button {
    private Receiver receiver;

    public ConcreteCommand(Receiver receiver) =>
        this.receiver = receiver;

    public void Execute() =>
        receiver.Action();
}
interface Button {
    void Execute();
}
class Program {
    static void Main() {
        // Création d'un objet récepteur
        Receiver receiver = new Receiver();

        // Création d'une commande concrète liée au récepteur
        ICommand command = new ConcreteCommand(receiver);

        // Création de l'invocateur et association de la commande
        Invoker invoker = new Invoker();
        invoker.SetCommand(command);

        // Exécution de la commande via l'invocateur
        invoker.ExecuteCommand();
    }
}
````
# [Iterator](#patron-de-conception-comportementaux)
Permet de parcourir les éléments d’une collection sans révéler sa représentation interne.
```csharp
class Iterator<T> {
    public Iterator<T> Next { get; private set; }
    public T Value { get; private set; }
    public Iterator<T> GetNext => Next;
    public bool HasNext => Next != null;
}
class IteratorBuilder {
    public static Iterator<T> BuildList(T ...values) {
        if (values.Length == 0) return null;
        var iterator = new Iterator {
            Value = value[0]
        };
        for (let index = 1; index < values.Length; index++) {
            iterator.Next = new Iterator {
                Value = value[index]                 
            };
            iterator = iterator.Next;
        }
    }
}
```
# [Mediator](#patron-de-conception-comportementaux)
Médiateur est un patron de conception comportemental qui diminue les dépendances chaotiques entre les objets. 

Il restreint les communications directes entre les objets et les force à collaborer uniquement via un objet médiateur.

Pour cela il centralise toute les informations.

````csharp
interface Mediator {
    void notify(Component sender, string event);
}
class AuthentificationDialog : Mediator {
    public string Title { get; set }
    public TextBox Username { get; }
    public TextBox Password { get; }
    TypeAuthentification Type { get; set; }
    public AuthentificationDialog (TextBox username, TextBox password) {
        Username = username;
        Username.Mediator = this;
        Password = password;
        Password.Mediator = this;
    }
    public void notify (Component sender, string event) {
        switch (event) {
            case "click": 
                if (sender.GetHashCode() == Password.GetHashCode())
                    VertifyPasswordSecurity(sender);
                break;
            case "keypress":
                // Do nothing                
                break;
        }
    }
    public void VertifyPasswordSecurity (string Text) {
    
    }
}
enum TypeAuthentification {
    LOGIN, REGISTRATION;
}
abstract class Component {
    Mediator Mediator { get; set; }
    abstract virtual public void click () {
        Mediator.notify(this, "click");
    }
    public virtual void keypress () {
        Mediator.notify(this, "keypress");        
    }
    // ... Other events
}
class TextBox : Component {
    string Text { get; set; }
}
````
# [Memento](#patron-de-conception-comportementaux)
Permet de prendre des instantanés de l’état d’un objet et pour possiblement re-utilisé cet état plus tard.

Comme un éditeur de texte avec la fonction ctrl+z

```csharp
class Editor {
    Snapshot<TextField> Snapshot = new(new());
    void CreateSnapShot() =>
        Snapshot.CreateSnapShot();        
    void Restore() =>
        Snapshot.Restore();    
}
class TextField : DeepCopy {
    string Text { get; set; }
    //...
    TextField DeepCopy() {
        return new {
            Text = Text
        };
    }
}
class Snapshot<T> where T : Copy<T> {
    public Stack<T> Stacks = new();
    public T Current { get; }
    public (T element)
        => Current = element;
    void CreateSnapShot() =>
        Stacks.Push(Current.DeepCopy());
    void Restore() => 
        Current = stacks.Pop();
}
interface Copy<T> {
    T DeepCopy();
}
```
# [Observer](#patron-de-conception-comportementaux)
L’Observateur permet de mettre en place un mécanisme de souscription pour envoyer des notifications à plusieurs objets, au sujet d’événements concernant les objets qu’ils observent.
```csharp
class Publisher<T> where T : Subscriber {
    Dictionnary<int, T> Subscribers = new();
    void Subscribe(T subscribe)
        => Subscriber[subscribe.GetUniqueID()] = subscribe;
    void NotifySubscribers() {
        for(T subscriber : Subscribers)
            subscriber.Notify();
    }
    void UnSubscribe(T subscriber)
        => Subscribers.Remove(subscriber);
}
interface Subscriber {
    void Notify();
    int GetUniqueID();
}
```
# [State](#patron-de-conception-comportementaux)
État est un patron de conception comportemental qui permet de modifier le comportement d’un objet lorsque son état interne change.

```csharp
class TextFieldCode {
    string Text { get; set; }
    TextFieldCodeState State = TextFieldCodeState.None;
    void Compiling () {
        State = TextFieldCodeState.Compiling;
        if (Text.Contains("//TODO"))
            State = TextFieldCodeState.Error;
        else
            State = TextFieldCodeState.Build;
    }
}
enum TextFieldCodeState {
    None, Compiling, Build, Error;
}
```
# [Strategy](#patron-de-conception-comportementaux)
Permet de définir une famille d’algorithmes, de les mettre dans des classes séparées et de rendre leurs objets interchangeables.
````csharp
interface ISortStrategy<T> {
    List<T> Sort(List<T> data);
}
class MergeSort<T> : ISortStrategy<T> where T : IComparable<T>
{
    public List<T> Sort(List<T> data)
    {
        // Implémentation du tri par fusion
        // (Remplacez cela par l'algorithme de tri réel)
        data.Sort();
        return data;
    }
}
// Stratégie de tri rapide
class QuickSort<T> : ISortStrategy<T> where T : IComparable<T>
{
    public List<T> Sort(List<T> data)
    {
        // Implémentation du tri rapide
        // (Remplacez cela par l'algorithme de tri réel)
        data.Sort();
        return data;
    }
}
// Contexte qui utilise la stratégie de tri
class SortContext<T>
{
    private ISortStrategy<T> sortStrategy;

    public SortContext(ISortStrategy<T> strategy)
    {
        this.sortStrategy = strategy;
    }

    public List<T> ExecuteSort(List<T> data)
    {
        return sortStrategy.Sort(data);
    }
}
````
# [Template Method](#patron-de-conception-comportementaux)
Permet de mettre le squelette d’un algorithme dans la classe mère, mais laisse les sous-classes redéfinir certaines étapes de l’algorithme sans changer sa structure.
````csharp
abstract class BoissonChaudTemplate
{
    // Template method qui définit la procédure de préparation de la boisson chaude
    public void PreparerBoissonChaud()
    {
        ChaufferEau();
        InfuserIngredient();
        VerserDansLaTasse();
        AjouterCondiments();
    }

    // Méthodes concrètes partagées par les sous-classes
    private void ChaufferEau()
    {
        Console.WriteLine("Chauffage de l'eau");
    }

    private void VerserDansLaTasse()
    {
        Console.WriteLine("Verser dans la tasse");
    }

    // Méthodes abstraites que les sous-classes doivent implémenter
    protected abstract void InfuserIngredient();
    protected abstract void AjouterCondiments();
}

// Sous-classe pour la création de thé
class The : BoissonChaudTemplate
{
    protected override void InfuserIngredient()
    {
        Console.WriteLine("Infusion du thé");
    }

    protected override void AjouterCondiments()
    {
        Console.WriteLine("Ajout de citron");
    }
}

// Sous-classe pour la création de café
class Cafe : BoissonChaudTemplate
{
    protected override void InfuserIngredient()
    {
        Console.WriteLine("Infusion du café moulu");
    }

    protected override void AjouterCondiments()
    {
        Console.WriteLine("Ajout de sucre et de lait");
    }
}

class Program
{
    static void Main()
    {
        Console.WriteLine("Préparation de thé :");
        BoissonChaudTemplate the = new The();
        the.PreparerBoissonChaud();

        Console.WriteLine("\nPréparation de café :");
        BoissonChaudTemplate cafe = new Cafe();
        cafe.PreparerBoissonChaud();
    }
}
````
# [Visitor](#patron-de-conception-comportementaux)
Permet de séparer les algorithmes et les objets sur lesquels ils opèrent.
```csharp
interface IVisiteur {
    void VisiterCercle(Cercle cercle);
    void VisiterCarre(Carre carre);
}
interface IElement {
    void Accepter(IVisiteur visiteur);
}
class Cercle : IElement {
    public double Rayon { get; }

    public Cercle(double rayon) {
        Rayon = rayon;
    }

    public void Accepter(IVisiteur visiteur) {
        visiteur.VisiterCercle(this);
    }
}
class Carre : IElement {
    public double Cote { get; }

    public Carre(double cote) {
        Cote = cote;
    }

    public void Accepter(IVisiteur visiteur) {
        visiteur.VisiterCarre(this);
    }
}
// Implémentation concrète du visiteur qui calcule la surface totale des formes géométriques
class CalculateurSurface : IVisiteur {
    public double SurfaceTotale { get; private set; }

    public void VisiterCercle(Cercle cercle) {
        SurfaceTotale += Math.PI * Math.Pow(cercle.Rayon, 2);
    }

    public void VisiterCarre(Carre carre) {
        SurfaceTotale += Math.Pow(carre.Cote, 2);
    }
}
```