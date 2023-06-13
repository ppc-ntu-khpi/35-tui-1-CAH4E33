# UI Lab 1
![](terminal-icon.png)
![](gui-icon.png)

## На "п'ять"
1. Перепишіть метод **ShowCustomerDetails** з використанням класів *Bank, Customer, Account* та ін. з наших попередніх лаб - інформація про клієнтів банку та їх рахунках має читатись з файлу **test.dat** (робота номер 8, [файл даних](https://github.com/liketaurus/TUI-Labs/blob/master/data/test.dat) також є в цьому ж репозиторію). Інформацію про клієнта (та про перший рахунок, що йому належить) ви маєте побачити, увівши номер клієнта.
2. Запустіть проект, впевніться, що все працює як очікувалось. Продемонстрируйте результат викладачеві.

* ## Код класу TUIdemo
```java
import data.DataSource;
import domain.*;
import jexer.TAction;
import jexer.TApplication;
import jexer.TField;
import jexer.TText;
import jexer.TWindow;
import jexer.event.TMenuEvent;
import jexer.menu.TMenu;

import java.io.IOException;

/**
 * @author Alexander 'Taurus' Babich
 */
public class TUIdemo extends TApplication {


    private static final int ABOUT_APP = 2000;
    private static final int CUST_INFO = 2010;

    public static void main(String[] args) throws Exception {


        TUIdemo tdemo = new TUIdemo();
        (new Thread(tdemo)).start();
    }

    public TUIdemo() throws Exception {
        super(BackendType.SWING);

        addToolMenu();
        //custom 'File' menu
        TMenu fileMenu = addMenu("&File");
        fileMenu.addItem(CUST_INFO, "&Customer Info");
        fileMenu.addDefaultItem(TMenu.MID_SHELL);
        fileMenu.addSeparator();
        fileMenu.addDefaultItem(TMenu.MID_EXIT);
        //end of 'File' menu  

        addWindowMenu();

        //custom 'Help' menu
        TMenu helpMenu = addMenu("&Help");
        helpMenu.addItem(ABOUT_APP, "&About...");
        //end of 'Help' menu 

        setFocusFollowsMouse(true);
        //Customer window
        ShowCustomerDetails();
    }

    @Override
    protected boolean onMenu(TMenuEvent menu) {
        if (menu.getId() == ABOUT_APP) {
            messageBox("About", "\t\t\t\t\t   Just a simple Jexer demo.\n\nCopyright © 2019 Alexander 'Taurus' Babich").show();
            return true;
        }
        if (menu.getId() == CUST_INFO) {
            try {
                ShowCustomerDetails();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
            return true;
        }
        return super.onMenu(menu);
    }

    private void ShowCustomerDetails() throws IOException {

        DataSource data = new DataSource("src/data/test.dat");
        data.loadData();
        TWindow custWin = addWindow("Customer Window", 2, 1, 40, 10, TWindow.NOZOOMBOX);
        custWin.newStatusBar("Enter valid customer number and press Show...");
        custWin.addLabel("Enter customer number: ", 2, 2);
        TField custNo = custWin.addField(24, 2, 3, false);
        TText details = custWin.addText("Owner Name: \nAccount Type: \nAccount Balance: ", 2, 4, 38, 8);
        custWin.addButton("&Show", 28, 2, new TAction() {
            @Override
            public void DO() {
                try {
                    String type;
                    int custNum = Integer.parseInt(custNo.getText());
                    Customer bal = Bank.getCustomer(custNum);
                    Account acc = bal.getAccount(0);

                    if (acc instanceof SavingsAccount) {
                        type = "Savings";
                    } else {
                        type = "Checking";
                    }

                    //details about customer with index==custNum
                    details.setText("Owner Name: " + bal.getFirstName() + " " + bal.getLastName() + " (id=" + custNum + ")\nAccount Type: " + type + "\nAccount Balance: $" + acc.getBalance() + "");
                } catch (Exception e) {
                    messageBox("Error", "You must provide a valid customer number!").show();
                }
            }
        });
    }
}
```
## Результат виконання програми
![](https://github.com/ppc-ntu-khpi/35-tui-1-CAH4E33/blob/master/Images/Result1306.png)

