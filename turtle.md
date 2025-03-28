# Imagine Logo from Temu

# Feladat leirasa :

    Egy egyszerusitett Imagine Logo wpfben megirva , tud forogni illetve menni erre arra

# A PROGRAM ISMERT HIBAJA

    Valami oknal fogva megjelenik egy masodik "teknoc" is a bal felso sarokba de egy cleareles utan eltunik

# FONTOS TUDNIVALOK

    A program angol nyelvu utasitasokat fogad el a jelenlegi allapotaban amik az alabbiak:
    - forward <szám>
    - backward <szám>
    - left <szám>
    - right <szám>
    - clear

# Tudnivalok a gridrol

1. Nagyon egyszeru grid mindossze egy canvas es egy textbox van benne , a textbox csak az utasitasok fogadasara alkalmas ha nem az elore megadott utasitasokat hasznaljuk akkor hibat dob
2. A CommandTextBox_KeyDown enter megnyomasa eseten bekuldi a programnak az adott utasitast.

```c#
<Window x:Class="ImagineLogoClone.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Application" Height="450" Width="800">
    <Grid>
        <Canvas Name="LogoCanvas" Background="White" VerticalAlignment="Stretch" HorizontalAlignment="Stretch">
            <Ellipse Name="Turtle" Width="20" Height="20" Fill="Black" />
        </Canvas>
        <TextBox Name="CommandTextBox" VerticalAlignment="Bottom" Height="30" Margin="10,0,10,10" KeyDown="CommandTextBox_KeyDown" />
    </Grid>
</Window>
```

3. A program indulasakor deklaraljuk az alapertelmezett valtozokat meg magat a "teknost" . A kezdeti poziciokat xPos es yPoskent vesszuk fel illetve a teknoc szoget egy angleben.

```c#
private double xPos = 400;  
        private double yPos = 200;  
        private double angle = 0;   

        private Ellipse turtle;
```

4. Ezek betoltese utan a Canvashoz hozzaadjuk illetve el is helyezzuk a teknost

```c#
 LogoCanvas.Children.Add(turtle);

            
            Canvas.SetLeft(turtle, xPos);
            Canvas.SetTop(turtle, yPos);
```

5. Ha valamilyen parancsot kuldunk be az enterrel akkor a textbox tartalmat trimmel szetszedjuk , lefuttassuk a hozza valo parancsot es toroljuk a textbox tartalmat.

```c#
private void CommandTextBox_KeyDown(object sender, KeyEventArgs e)
        {
            if (e.Key == Key.Enter)
            {
                string command = CommandTextBox.Text.Trim();
                ExecuteCommand(command);
                CommandTextBox.Clear();
            }
        }
```

6. Mivel a parancsunkat szet vagtuk ezert ez tobb reszre fog esni tetelezzuk fel hogy a userunk nem degeneralt ezert nem ugy irja le hogy "f o r w a r d" (nem is engedi) ezert egy string tombbe felvesszuk a commandod szokoz alapjan splitelve es hozzaadjuk egy stringhez ami ennek az elso reszet fogja atvenni ami jo esetbe a megadott dolgok kozott van (forward)

```c#
string[] parts = command.Split(' ');
string action = parts[0].ToLower();
```

7. az ExecuteCommand metodus nem mas mint egy elegge nagy morzsanyi switch ami a casehez megfeleloen irkalja a koordinatat es kuldi tovabb a MoveTurtle fuggvenynek. Mivel van egy part valtozonk es annak a masodik (jelenesetben 1. indexu eleme ) eleme az a szam amivel kell mozgatni a teknocunket az if agakban azt csekkoljuk hogy int-e alakithato e. Az alapertelmezett esettel pedig biztositjuk hogy veletlenul se egy elme zakkant probalja hasznalni a programot es ha nincs benne a casek kozott akkor hibat dob.

```c#
switch (action)
                case "forward":
                    if (parts.Length > 1 && double.TryParse(parts[1], out double forwardDistance))
                    {
                        MoveTurtle(forwardDistance);
                    }
                    break;
                case "backward":
                    if (parts.Length > 1 && double.TryParse(parts[1], out double backwardDistance))
                    {
                        MoveTurtle(-backwardDistance);
                    }
                    break;
                case "left":
                    if (parts.Length > 1 && double.TryParse(parts[1], out double leftAngle))
                    {
                        TurnTurtle(-leftAngle);
                    }
                    break;
                case "right":
                    if (parts.Length > 1 && double.TryParse(parts[1], out double rightAngle))
                    {
                        TurnTurtle(rightAngle);
                    }
                    break;
                case "clear":
                    ClearCanvas();
                    break;
                default:
                    MessageBox.Show("Ismeretlen parancs!");
                    break;
```

8. A teknoc mozgatasara a MoveTurtle metodus van hasznalatba aminek atadunk egy double tipusu valtozot ami a tavolsagot tarolja el , eltaroljuk a ketto regi poziciot majd a fokot is kiszamoljuk.
Ezt kovetoen bonyolult matematikai egyenletekkel kiszamoljuk az uj poziciokat.

```c#
double oldXPos = xPos;
            double oldYPos = yPos;

            
            double radianAngle = angle * Math.PI / 180;
            xPos += distance * Math.Cos(radianAngle);
            yPos += distance * Math.Sin(radianAngle);

//a kod egy pontot mozgat egy adott szogben illetve tavolsagba , a szoget fokbol radianba alakitja majd a cos és sinnel kiszamolja mennyit kell menni X,Y iranyba.
```

9. Hogy a teknos vonalat is huzzon maga utan es az latszodjon is ezert egy Line-t deklaralunk a regi es a jelenlegi x koordinatakkal illetve beallitva hogy fekete legyen maga a line.

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

10. A Canvashoz hozzaadjuk a linet a megfelelo poziciokhoz.

```c#
LogoCanvas.Children.Add(line);

Canvas.SetLeft(turtle, xPos);
Canvas.SetTop(turtle, yPos);
```

11. A teknos forgatasahoz egy TurnTurtle fuggveny van hasznalva amiben az anglehez hozza adjuk a megadott right / left erteket.

```c#
private void TurnTurtle(double turnAngle)
        {
            angle += turnAngle;
        }
```

12. A Clear pedig minden poziciot vissza allit alapertekbe es a vonalat is torli.

```c#
private void ClearCanvas()
        {
            LogoCanvas.Children.Clear();
            LogoCanvas.Children.Add(turtle); 
            xPos = 400;
            yPos = 200;
            angle = 0;
            Canvas.SetLeft(turtle, xPos);
            Canvas.Set
        }
```

