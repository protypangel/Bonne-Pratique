# [Patron de conception créationnels](../Design-Pattern.md)
* [Factory Simple](#factory-simple) 
* [Factory Methode](#factory-methode)
* [Factory Abstraite](#factory-abstraite)
* [Builder](#builder)
* [Clone](#clone)
* [Singleton](#singleton)

# [Factory Simple](#patron-de-conception-créationnels)
Une seul fabrique est utilisé pour créer plusieurs instance différentes de l'interface.
```csharp
interface Phone {
    string Latest();
}
class Apple : Phone {
    string Latest () => "IPhone 15 Pro Max";
}
class Samsung : Phone {
    string Latest () => "S24 Ultra";
}
// Factory
class ProductFactory {
    Product CreatePhone (string name) {
        if (name == "Samsung") return new Samsung();
        if (name == "Apple") return new Apple();
        throw new Exception($"{name} is not reconized");
    }
}
```
```csharp
class User {
    string Name     { get; set; }
    string Password { get; set; }
}
class Admin  : User {}
class Seller : User {}
// Factory
class UserFactory {
    User CreateUser<T> (string name, string password) where T : User {
        T user = new {
            Name = name,
            Password = password
        };
        return user;
    }
}
```
# [Factory Methode](#patron-de-conception-créationnels)
Permet d'avoir plusieurs fabriques où chacune fabrique créer leurs propres.

Cela permet de respecter les principes SOLID, qui sont nécéssaire à la maintenabilité du code, la où `les fabriques simple` peuvent être modfié à chaque fois.
```csharp
interface Phone {
    string Latest();
}
class Apple : Phone {
    string Latest () => "IPhone 15 Pro Max";
}
class Samsung : Phone {
    string Latest () => "S24 Ultra";
}
// Factory
interface ProductFactory {
    Phone CreatePhone ();
}
class SamsungFactory {
    Phone CreatePhone () => new Samsung();
}
class AppleFactory {
    Phone CreatePhone () => new Apple();
}
```
```csharp
class RGB {
    byte R { get; set; }
    byte G { get; set; }
    byte B { get; set; }
}
abstract class Image {
    List<RGB> RGB = new ();
    void Save();
}
class JPG : Image {
    void Save () { }
}
class PNG : Image {
    void Save () { }
}
// Factory
interface ImageFactory {
    Image Create();
}
class JPGFactory {
    Image Create() => new JPG();
}
class PNGFactory {
    Image Create() => new PNG();
}
```
# [Factory Abstraite](#patron-de-conception-créationnels)
Permet d'avoir une factory qui peut créer plusieurs instance d'une même fabrique.

Utile quand on a une combinaisons tel que `Phone` et `Televiseur`
```csharp
// Phone
interface Phone {
    string Name();
}
class IPhone : Phone {
    string Name () => "IPhone 15 Pro Max";
}
class Samsung : Phone {
    string Latest () => "S24 Ultra";
}
// Televiseur
interface Televiseur {
    string Name();
}
class IMac : Televiseur {
    string Name () => "IMac 27''";
}
class Oddyssey : Televiseur {
    string Name () => "Oddyssey G9";
}
// Factory
interface ProductFactory {
    Phone CreatePhone();
    Televiseur CreateTeleviseur();
}
class SamsungFactory {
    Phone CreatePhone () => new Samsung();
    Televiseur CreateTeleviseur() => new Oddyssey();
}
class AppleFactory {
    Phone CreatePhone () => new Apple();
    Televiseur CreateTeleviseur() => new IMac();
}
```
# [Builder](#patron-de-conception-créationnels)
Créer un objet dont les paramètres sont facultatifs.
```csharp
class Coordonnee {
    int X = 0 { get; set; }
    int Y = 0 { get; set; }
}
class Personnage {
    int PointDeVie { get; set; }
    int DegatPhysique { get; set; }
    int DegatMagique { get; set; }
    int ArmurePhysique { get; set; }
    int ArmureMagique { get; set; }
    Coordonnee Cordonnee { get; set; }
}
// Builder
class PersonnageBuilder {
    Personnage Personnage = new {
        PointDeVie = 10;
        DegatPhysique = 1;
        DegatMagique = 0;
        ArmurePhysique = 0;
        ArmureMagique = 0;
        Cordonnee = new();
    };
    PersonnageBuilder SetCoordonnee (Coordonnee Coordonnee) {
        Personnage.Coordonnee = Coordonnee;
        return this;        
    }
    PersonnageBuilder SetPointDeVie(int pointDeVie) {
        Personnage.PointDeVie = pointDeVie;
        return this;        
    }
    Personnage Build () => Personnage;
}
```
# [Clone](#patron-de-conception-créationnels)
Permet d'avoir une copie de l'objet, pour ne pas modifier cet objet en lui meme.
```csharp
class HTMLNode {
    private static Dictionnary<int, Balise> Balises = new();
    public static Balise GetCopy(int id) {
        Balise balise = Balises[id];
        return new {
            Name = balise.Name,
            Content = balise.Content
        };
    }
}
class Balise {
    string Name { get; set; }
    string Content { get; set; }
}
```
# [Singleton](#patron-de-conception-créationnels)
Utiliser une seul instance d'une classe
```csharp
class SingletonClientController {
    private static ClientController Controller;
    public static ClientController Get () {
        if (Controller == null) Controller = new();
        return Controller;
    }
}
class ClientController {
}
```
