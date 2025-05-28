# Imagine Logo from Temu

# Feladat leírása :

    Egy egyszerűsített Imagine Logo wpfben megirva , tudja az alap dolgokat perpilanat még tollatfel és tollatle nélkül


# FONTOS TUDNIVALOK

    A program által elfogadott utasítások:
    - előre <szám>
    - hátra <szám>
    - balra <szám>
    - jobbra <szám>
    - töröl

# Tudnivalók a gridről

1. Nagyon egyszerű grid mindössze egy canvas és egy textbox van benne , a textbox csak az utasítások fogadására alkalmas ha nem az előre megadott utasításokat használjuk akkor hibát dob
2. A CommandTextBox_KeyDown enter megnyomása esetán beküldi a programnak az adott utasítást.

```c#
<Window x:Class="ImagineLogoClone.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Application" Height="450" Width="800">
    <Grid>
        <Canvas Name="LogoCanvas" Background="White" VerticalAlignment="Stretch" HorizontalAlignment="Stretch">
        </Canvas>
        <TextBox Name="CommandTextBox" VerticalAlignment="Bottom" Height="30" Margin="10,0,10,10" KeyDown="CommandTextBox_KeyDown" />
    </Grid>
</Window>
```

3. A program indulásakor deklaráljuk az alapértelmezett változókat meg magát a "teknőst" . A kezdeti pozíciókat xPos és yPoskent vesszük fel illetve a teknőc szögét egy angleben.

```c#
private double xPos = 400;  
        private double yPos = 200;  
        private double angle = -90;   

        private Ellipse turtle;
```

4. Ezek betöltése után a Canvashoz hozzáadjuk illetve el is helyezzük a teknőst

```c#
 turtle = new Ellipse
 {
     Width = 20,
     Height = 20,
     Fill = Brushes.Black
 };
 LogoCanvas.Children.Add(turtle);

            
            Canvas.SetLeft(turtle, xPos);
            Canvas.SetTop(turtle, yPos);
```

5. Ha valamilyen parancsot küldünk be az enterrel akkor a textbox tartalmát trimmel szétszedjük , lefuttassuk a hozzá való parancsot es töröljük a textbox tartalmát.

```c#
private void CommandTextBox_KeyDown(object sender, KeyEventArgs e)
{
    if (e.Key == Key.Enter)
    {
        string command = CommandTextBox.Text.Trim();
        CommandExecute(command);
        CommandTextBox.Clear();
    }
}
```

6. Mivel a parancsunkat szét vágtuk ezért ez több részre fog esni tételezzük fel hogy a felhasználónk nem degenerált ezért nem úgy írja le hogy "e l ő r e" (nem is engedi) ezért egy string tömbbe felvesszük a commandot szóköz alapján splitelve és hozzáadjuk egy stringhez ami ennek az első részét fogja átvenni ami jó esetbe a megadott dolgok között van (előre)

```c#
string[] parts = command.Split(' ');
string action = parts[0].ToLower();
```

7. az ExecuteCommand metódus nem más mint egy eléggé nagy morzsányi switch ami a casehez megfelelően irkálja a koordinátát és küldi tovább a MoveTurtle függvénynek. Mivel van egy part változónk és annak a második (jelenesetben 1. indexű eleme ) eleme az a szám amivel kell mozgatni a teknőcünket az if ágakban azt csekkoljuk hogy int-é alakítható-e. Az alapértelmezett esettel pedig biztosítjuk hogy véletlenül se egy elme zakkant próbálja használni a programot és ha nincs benne a casek között akkor hibát dob.

```c#
 if (command == "töröl")
 {
     //ClearCanvas();
 }


 switch (action)
 {
     case "előre":
         MoveTurtle(volume);
         break;
     case "hátra":
         MoveTurtle(-volume);
         break;
     case "balra":
         //TurnTurtle(-volume)
         break;
     case "jobbra":
         //TurnTurtle(volume);
         break;
     default:
         MessageBox.Show("Ismeretlen parancs!");
         break;
 }
```

8. A teknőc mozgatására a MoveTurtle metódus van használatba aminek átadunk egy double tipusú változót ami a távolságot tárolja el. Eltároljuk a kettő régi pozíciót majd a fokot is kisáamoljuk.
Ezt követően bonyolult matematikai egyenletekkel kisáamoljuk az új pozíciókat.

```c#
double oldXPos = xPos;
            double oldYPos = yPos;

            
            double radianAngle = angle * Math.PI / 180;
            xPos += distance * Math.Cos(radianAngle);
            yPos += distance * Math.Sin(radianAngle);

//a kod egy pontot mozgat egy adott szogben illetve tavolsagba , a szoget fokbol radianba alakitja majd a cos és sinnel kiszamolja mennyit kell menni X,Y iranyba.
//Radian az azer kell mer hogy a .Sin es a .Cos ebben varja el a szoget nem fokba
//1 radian az a szog ami a koriv hossza megegyezik a kor sugaraval
//360 fok = 1(pi) radian
```

9. Hogy a teknos vonalat is húzzon maga után és az látszódjon is ezért egy Line-t deklarálunk a régi és a jelenlegi x koordinátákkal illetve beállítva hogy fekete legyen maga a line.

```c#
 Line line = new Line
            {
                X1 = oldXPos,
                Y1 = oldYPos,
                X2 = xPos,
                Y2 = yPos,
                Stroke = Brushes.Black,
                StrokeThickness = 2
            };
```

10. A Canvashoz hozzáadjuk a linet a megfelelő pozíciókhoz.

```c#
LogoCanvas.Children.Add(line);

Canvas.SetLeft(turtle, xPos);
Canvas.SetTop(turtle, yPos);
```

11. A teknős forgatásához egy TurnTurtle függvény van használva amiben az anglehez hozzá adjuk a megadott right / left értéket.

```c#
private void TurnTurtle(double turnAngle)
        {
            angle += turnAngle;
        }
```

12. A Clear pedig minden pozíciót vissza állít alapértékbe és a vonalat is törli.

```c#
 private void ClearCanvas()
        {
            LogoCanvas.Children.Clear();
            LogoCanvas.Children.Add(turtle);
            CanvasInit();
        }
```

13. A Clearen belül látható init pedig a Canvas betöltésére funckionál.

```c#
 private void CanvasInit()
        {
            _xPos = 400;
            _yPos = 200;
            _angle = -90;
            Canvas.SetLeft(turtle, _xPos - 10);
            Canvas.SetTop(turtle, _yPos - 10);
        }
```

<details>
<summary>Nyisd le a xaml file forrásáért!</summary>
### MainWindow.xaml tartalma

```c#
<Window x:Class="Wpf_1_Imagine.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:Wpf_1_Imagine"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <Grid>
        <Canvas x:Name="LogoCanvas" Background="White" VerticalAlignment="Stretch" HorizontalAlignment="Stretch">
        </Canvas>
        <TextBox x:Name="CommandTextBox" VerticalAlignment="Bottom" Height="30" Margin="10,0,10,10" KeyDown="CommandTextBox_KeyDown" />
    </Grid>
</Window>
```
</details>

<details>
<summary>Nyisd le a .xaml.cs file tartalmáért!</summary>
### MainWindow.xaml.cs tartalma

```c#
using System.Text;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Navigation;
using System.Windows.Shapes;

namespace Wpf_1_Imagine
{
    /// <summary>
    /// Interaction logic for MainWindow.xaml
    /// </summary>
    public partial class MainWindow : Window
    {
        private double _xPos = 0;
        private double _yPos = 0;
        private double _angle = 0;

        private Ellipse turtle;

        public MainWindow()
        {
            InitializeComponent();
            MessageBox.Show("A program az alabbi parancsokat fogadja el : \nelőre <lepesek száma>\nhátra <lepesek száma>\njobbra <fok>\nbalra <fok>\n töröl ");

            turtle = new Ellipse
            {
                Width = 20,
                Height = 20,
                Fill = Brushes.Black
            };
            LogoCanvas.Children.Add(turtle);

            CanvasInit();
        }

        private void CommandTextBox_KeyDown(object sender, KeyEventArgs e)
        {
            if (e.Key == Key.Enter) 
            {
                string command = CommandTextBox.Text.Trim();
                CommandExecute(command);
                CommandTextBox.Clear();
            }
        }

        private void CommandExecute(string command)
        {
            if (command == "töröl")
            {
                ClearCanvas();
            }

            string[] parts = command.Split(' ');
            if (parts.Length != 2)
            {
                return;
            }
            string action = parts[0].ToLower();
            if (!(double.TryParse(parts[1], out double volume)))
            {
                return;
            }

            switch (action)
            {
                case "előre":
                    MoveTurtle(volume);
                    break;
                case "hátra":
                    MoveTurtle(-volume);
                    break;
                case "balra":
                    TurnTurtle(-volume);
                    break;
                case "jobbra":
                    TurnTurtle(volume);
                    break;
                default:
                    MessageBox.Show("Ismeretlen parancs!");
                    break;
            }
        }

        private void MoveTurtle(double distance)
        {
            double oldXPos = _xPos;
            double oldYPos = _yPos;

            double radianAngle = _angle * Math.PI / 180;
            _xPos += distance * Math.Cos(radianAngle);
            _yPos += distance * Math.Sin(radianAngle);

            Line line = new Line
            { 
                X1 = oldXPos,
                Y1 = oldYPos,
                X2 = _xPos,
                Y2 = _yPos,
                Stroke = Brushes.Black,
                StrokeThickness = 2
            };
            
            LogoCanvas.Children.Add(line);

            Canvas.SetLeft(turtle, _xPos-10);
            Canvas.SetTop(turtle, _yPos-10);

        }

        private void TurnTurtle(double volume)
        {
            _angle += volume;
        }

        private void ClearCanvas()
        {
            LogoCanvas.Children.Clear();
            LogoCanvas.Children.Add(turtle);
            CanvasInit();
        }

        private void CanvasInit()
        {
            _xPos = 400;
            _yPos = 200;
            _angle = -90;
            Canvas.SetLeft(turtle, _xPos - 10);
            Canvas.SetTop(turtle, _yPos - 10);
        }
    }
}
```    
</details>
