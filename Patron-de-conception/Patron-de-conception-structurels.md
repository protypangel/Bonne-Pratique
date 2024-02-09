# [Patron de conception structurel](../Design-Pattern.md)
* [Adapter](#adapter)
* [Bridge](#bridge)
* [Composite](#composite)
* [Decorator](#decorator)
* [Facade](#facade)
* [Flyweight](#flyweight)
* [Proxy](#proxy)

# [Adapter](#patron-de-conception-structurel)
L'adapter permet de faire interrargir deux classe qui ne sont pas compatible.

Par exemple `Balise` et `UserDetails` ne sont pas compatible, mais nous avons besoin des deux pour créer une page utilisateur.

Alors nous créer `UserAdapter` qui permet d'écrire le code HTML en fonction des données utilisateur.
````csharp
abstract class Balise {
    abstract string WriteHtml ();
}
class UserAdapter : Balise {
    UserDetails Details;
    string WriteHtml () 
        => $"<p>{Details.UserName()}</p><input type=\"checkbox\" {Details.IsActive()?'checked':''}/><label>IsActive</label>"
}
interface UserDetails {
    bool IsActive();
    string UserName();
}
class User : UserDetails {
    override bool isActive () => true;
}
````
# [Bridge](#patron-de-conception-structurel)
Permet de séparer une grosse classe ou un ensemble de classes connexes en deux hiérarchies, abstraction et implémentation, qui peuvent évoluer indépendamment l’une de l’autre.

Exemple, `Personnage` et `Outil`sont deux abstractions; et `Monstre`, `Joueur`, `Epee` et `Bouclier` sont des impléméntation.
```csharp
// Forme
class Personnage {
    protected Outil Outil { get; set; }
}
class Monstre : Personnage {
}
class Joueur : Personnage {
}
// Couleur
abstract class Outil {
    abstract void Action();
}
class Epee : Outil {
    void Action() {
        Console.Write("Attaque");
    }
}
class Bouclier : Outil {
    void Action() {
        Console.Write("Defendre");
    }
}
```
# [Composite](#patron-de-conception-structurel)

Facilite l'organisation d'objets au sein d'arborescences, permettant ainsi de les traiter individuellement.

Tout en nécessitant que les éléments partagent un parent commun (`Node`) et que les enfants soient hiérarchiquement structurés, comme c'est le cas avec une `Feuille` au sein d'un `ArbreBinaire`.
```csharp
interface Node {
    List<Node> Chercher(int valeur);
}
class Feuille : Node {
    int Valeur { get; set; }
    List<Node> Chercher(int valeur) {
        if (Valeur == valeur) return new List<Node> { this };
        throw new Exception();
    }
}
class AbreBinaire : Node {
    readonly Node Gauche, Droite;
    readonly int ID;
    public AbreBinaire(Node gauche, Node droite, int ID) {
        Gauche = gauche;
        Droite = droite;
    }
    List<Node> Chercher(int valeur) {
        try {
            List<Node> gauche = Gauche.Chercher(valeur);
            gauche.Append(this);
            return gauche;
        } catch (Exception e) {
        }
        try {
            List<Node> droite = Droite.Chercher(valeur);
            droite.Append(this);
            return droite;
        } catch (Exception e) {
        }
        throw new Exception($"It's not in the node {ID}");
    }
}
```
# [Decorator](#patron-de-conception-structurel)
Affecter dynamiquement de nouveaux comportements à des objets en les plaçant dans des emballeurs qui implémentent ces comportements.

Par exemple : `Sugar` appellera `Cream` qui appellera `Espresso`
```csharp
interface Coffee {
    void Make();
}
abstract class Espresso : Coffee {
    private Coffee Coffee;
    public Espresso (Coffee coffee) {
        Coffee = coffee;
    }
    public virtual void Make () {
        if (Coffee != null) {
            Coffee.Make();
        }
        Console.WriteLine("The coffee is maked");
    }
}
class Cream : Espresso {
    public Cream (Coffee coffee) : base(coffee) {}
    public virtual void Make () {
        Console.WriteLine("Cream has been added");
        base.Make();
    }
}
class Sugar : Cream {
    public Sugar (Coffee coffee) : base(coffee) {}
    public virtual void Make () {
        Console.WriteLine("Sugar has been added");
        base.Make();
    }
}
```

# [Facade](#patron-de-conception-structurel)
Point d'accès plus facile à utiliser pour les utilisateur du code.
```csharp
public class UserService
{
    private readonly UserRepository User = new();
    private readonly RoleRepository Role = new();

    public bool IsActive(string username, string password) {
        UserDetails details = new (username, password);
        User? user = User.FindUserDetails(details);
        if (user == null)
            throw new UserNotFound(username, password);
        return user.IsActive        
    }

    public void CreateUser(UserDetails details) {
        Role.Create(details);
        User.Create(details);
    }

    public void DeleteUser(UserDetails details) {
        User? user = User.FindUserDetails(details);
        if (user != null)
            User.Delete(details);
    }
}
```
# [Flyweight](#patron-de-conception-structurel)
Permet de minimiser l’utilisation de la mémoire

# [Proxy](#patron-de-conception-structurel)
Un proxy agit comme un substitut au code original
```csharp
class RealSubject {
    public virtual void Request () {
    }    
}
class Proxy : RealSubject {
    private RealSubject Subject;
    public Proxy (RealSubject subject) {
        Subject = subject;
    }
    public override void Request () {
        if (CheckAccess())
        {
            Subject.Request();
            LogAccess();
        }
    }
    public bool CheckAcess () => true;
    public void LogAccess () => Console.Write("Proxy has been used");
}
class Client {
    public void ClientCode(RealSubject subject) => subject.Request();
}
class Program {
    static void Main(string[] args) {
        Client client = new();
        RealSubject subject = new();
        // Sans Proxy
        client.ClientCode(subject);
        // Avec Proxy
        client.ClientCode(new Proxy(subject));
    }
}
```
