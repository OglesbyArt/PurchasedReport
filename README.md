
package artpricingsystem;
import java.io.*;
import java.util.*;
import java.util.Comparator;
import java.util.Collections;
        
public class PurchasedReport {      
    public static int lineCount;
    public static void printReport ()
    {
        double totalActualPurchasePrice=0;
        double totalMaxPurchasePrice=0;
        int count=0;
        try
        {
            //sortFile();
            BoughtPainting tempPainting = new BoughtPainting();
            File  paintingFile = new File ("GalleryPaintings.dat");
            
            if (paintingFile.exists())
            {
                RandomAccessFile inFile = new RandomAccessFile (paintingFile, "r");
                while (inFile.getFilePointer()!=inFile.length())
                {
                    if (((lineCount%20) == 0) && (lineCount!=0))
                    {
                        System.out.println("\n\n\n\n");
                        printHeader();

                    }
                    if ((lineCount%20) == 0)
                    {
                        printHeader();
                    }
                    tempPainting.read (inFile);
                    printRecord(tempPainting);
                    boolean printed=printRecord(tempPainting);
                        if(printed)
                        {
                            totalActualPurchasePrice+=tempPainting.getActualPurchasePrice();
                            totalMaxPurchasePrice+=tempPainting.getSuggestedMaximumPurchasePrice();
                            count++;
                        }
                }
                inFile.close ();
                computeRatio(totalActualPurchasePrice, totalMaxPurchasePrice, count);
                count=0;
            }
            else
            {
              System.out.println ("\nNo Paintings have been purchased in the past year.");
            }

            System.out.println("Press <ENTER> to return to Main Menu");
            UserInterface.pressEnter();

        }
        catch (Exception e)
        {
            System.out.println ("***** Error: PurchasedReport.printReport () *****");
            System.out.println ("\t" + e);
        }
    } 
    
    public static boolean printRecord (BoughtPainting b)
    {
        Date today=new Date();
        Calendar calendar = Calendar.getInstance();
        int lastYear=calendar.get(Calendar.YEAR)-1;
        int thisMonth=calendar.get(Calendar.MONTH)+1;
        int thisDay=calendar.get(Calendar.DAY_OF_MONTH);
        String ytd=thisMonth+"/"+thisDay+"/"+lastYear;
        Date lowerbound = new Date (ytd);
        if(b.dateOfPurchase.after(lowerbound))
        {    
            System.out.printf ("%-25s%-35s%-25s%-45s%-35s%-30s", b.classification, b.dateOfPurchase, b.artistLastName, b.titleOfWork, b.suggestedMaximumPurchasePrice, b.actualPurchasePrice);
            if(b.actualPurchasePrice>b.suggestedMaximumPurchasePrice)
                System.out.printf ("%-35s\n", true);  
            else System.out.printf ("%-35s\n",false);  
            ++lineCount;
            return true;
        }
        return false;
    }
    
    public static void printHeader()
    {
        System.out.println ("\t\t\t\t\t       Produce Paintings Purchased in the Past Year Report       \n");
        System.out.printf ("%-25s%-35s%-25s%-45s%-35s%-30s%-35s\n", "Classification Type", "Purchase Date", "Artist Last Name", "Title of Work", "Suggested Max Purchase Price", "Actual Purchase Price", "Actual Price > Suggested Price Flag");
        System.out.println ("----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------");
        lineCount+=4;
    }
    
    public static void computeRatio(double actual, double max, int count)
    {
        double actualavg=actual/count;
        actualavg=Math.round(actualavg*100);
        actualavg=actualavg/100;
        System.out.print("\n\nThe average Actual Purchase Price is: $" + actualavg+ "\t\t");
        double maxavg=max/count;
        maxavg=Math.round(maxavg*100);
        maxavg=maxavg/100;
        System.out.print("The average Suggested Maximum Purchase Price is: $"+maxavg + "\t\t");
        double ratio=actualavg/maxavg;
        ratio=Math.round(ratio*100);
        ratio=ratio/100;
        System.out.println("The average ratio of the Actual Purchase Price to the Suggested Maximum Purchase Price is: " + ratio+"\n");
    }

    /*public static void sortFile() 
    {
       try{
           ArrayList <BoughtPainting> bp=new ArrayList();
           File paintingsFile = new File ("GalleryPaintings.dat");	
           RandomAccessFile oldFile = new RandomAccessFile (paintingsFile, "r");
           BoughtPainting tempPainting = new BoughtPainting ();
           BoughtPainting tempPainting2= new BoughtPainting ();
           while (oldFile.getFilePointer () != oldFile.length ()) 
           {
                tempPainting.read(oldFile);
                tempPainting.print();
                bp.add(tempPainting);
                tempPainting2.read(oldFile);
                tempPainting2.print();
                bp.add(tempPainting2);
           }     
                    /*bp.add(tempPainting);
                int i=0;
                while(i<3)
                {   
                    System.out.println(bp);
                    tempPainting.updateClassification();
                    tempPainting.updateDateOfPurchase();
                    ++i;
                    bp.add(tempPainting);
                }                    
            //}  
           Collections.sort(bp, BoughtPainting.comparator); 
           bp.stream();
       }
       catch (Exception e)
        {
            System.out.println ("***** Error: PurchasedReport.sortFile () *****");
            System.out.println ("\t" + e);
        }
       
    }*/
}
