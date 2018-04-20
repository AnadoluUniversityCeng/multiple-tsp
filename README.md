# The Multiple Traveling Salesman Problem

The [Multiple Traveling Salesman Problem](https://neos-guide.org/content/multiple-traveling-salesman-problem-mtsp) (*m*TSP) in which more than one salesman is allowed is a generalization of the Traveling Salesman Problem (TSP).
Given a set of cities, one depot (where *m* salesmen are located), and a cost metric, the objective of the *m*TSP is to determine a set of routes for *m* salesmen so as to minimize the total cost of the *m* routes. 
The cost metric can represent cost, distance, or time. The requirements on the set of routes are:

* All of the routes must start and end at the (same) depot.
* There must be at least one city (except depot) in each route.
* Each city must be visited exactly once by only one salesman.

**Multiple depots**: Instead of one depot, the multi-depot *m*TSP has a set of depots, with m<sub>j</sub> salesmen at each depot *j*. 
In the *fixed destination* version, a salesman returns to the same depot from which he started.

## Part-I

In this part of the homework, we will generate 100,000 random solutions to the *fixed destination* version of the multi-depot *m*TSP.
The number of depots and salesman per depot will be our parameters. The cost metric will be total distance in kilometers.
At the end, we will print the best solution that has the minimum cost among 100,000 random solutions.

Your project **must** be a valid maven project. `mvn clean package` must produce an executable jar file named **mTSP.jar** under the target directory.
This can be done via maven plugins such as [shade](https://maven.apache.org/plugins/maven-shade-plugin) or [assembly](https://maven.apache.org/plugins/maven-assembly-plugin) plugin.
Optional parameter [finalName](https://maven.apache.org/plugins/maven-shade-plugin/shade-mojo.html#finalName) can be used to change the name of the shaded artifactId.
To parse command line arguments, you *must* use [JewelCLI](http://jewelcli.lexicalscope.com) library.

For example, `java -jar target/mTSP.jar -d 5 -s 2 -v` would produce something like below.
Notice that the last line includes the cost metric: the total distance travelled by all salesmen.

```yaml
Depot1: İÇEL
  Route1: ZONGULDAK,GİRESUN,VAN,OSMANİYE,BİNGÖL,ELAZIĞ,ŞIRNAK,BAYBURT,IĞDIR
  Route2: BURDUR,AYDIN,MANİSA,TUNCELİ,ANKARA,ÇANKIRI,KIRIKKALE
Depot2: DİYARBAKIR
  Route1: KIRŞEHİR,KAYSERİ,KÜTAHYA,ARTVİN,İZMİR,HATAY,UŞAK,ISPARTA,KAHRAMANMARAŞ,İSTANBUL
  Route2: KONYA,ŞANLIURFA,ADIYAMAN,MALATYA,SİVAS,BATMAN,MUŞ,SİİRT
Depot3: ERZURUM
  Route1: AĞRI,KARAMAN,BOLU,ANTALYA,KASTAMONU,ÇORUM,ÇANAKKALE,SAKARYA,GÜMÜŞHANE,BİTLİS
  Route2: ERZİNCAN,GAZİANTEP,BURSA,HAKKARİ
Depot4: ESKİŞEHİR
  Route1: MUĞLA,BARTIN,NİĞDE,RİZE,NEVŞEHİR
  Route2: YOZGAT,KARABÜK,BALIKESİR,TEKİRDAĞ,AFYON,YALOVA
Depot5: TOKAT
  Route1: DÜZCE,TRABZON,MARDİN,ARDAHAN,KARS,ORDU,KOCAELİ,DENİZLİ,KIRKLARELİ,EDİRNE
  Route2: AKSARAY,BİLECİK,ADANA,SİNOP,AMASYA,KİLİS,SAMSUN
**Total cost is 52308
```

Non-verbose example `java -jar target/mTSP.jar -d 2 -s 5` will print city indices instead of city names:
```yaml
Depot1: 18
  Route1: 32,67,27,7,54,6,38,53,73
  Route2: 56,9,72,55,1,12
  Route3: 8,16,19,26,3,29,47,11,24
  Route4: 49,42,25,58,4,22
  Route5: 0,43,77,36,70
Depot2: 59
  Route1: 51,35,62,57,50
  Route2: 13,80,31,71,75,14,78
  Route3: 30,41,79,48,64,28,39,45,46
  Route4: 61,76,5,68,74,60,33,21,10,65,23
  Route5: 44,40,15,66,63,34,52,37,17,2,20,69
**Total cost is 51631
```


:exclamation: If you don't follow the aforementioned conventions, you will receive grade of zero (even if you think that your code works perfectly).

## Part-II

In the second part of the homework, we will apply a heuristic algorithm to our *fixed destination* version of the multi-depot *m*TSP.

The term heuristic is used for algorithms which find solutions among all possible ones, but they do not guarantee that the optimal will be found.
[Heuristic algorithms](https://optimization.mccormick.northwestern.edu/index.php/Heuristic_algorithms) often times used to solve *NP*-complete problems.

The heuristic will **iteratively** work on the solution (best of the 100,000 random solutions) obtained from the Part-I. 
In Part-II, we will define five different move operations, which will be detailed in the following subsections.
In each iteration, one move operation will be selected (among five) based on a random manner, and then it will be applied to the current solution.
If the move improves the solution (i.e., lessen the total distance travelled) then, we will update the best solution at hand. If not, next iteration will be continued.
To implement this logic, you need to devise a strategy to somehow backup the current solution. 
So that if the move operation does not improve the solution, it should be possible to rollback to a previous state.
It is recommended to do a reach on the Internet using the following keywords: *copy constructor*, *deep cloning*, *shallow cloning*, and *marshalling*.
It is totally up to you to how to implement this logic, you can even write an method that calculates a cost function without applying the move!

### Move operations
Some of the the move operation will involve a process where you need to generate two different random numbers from a given interval.
Please write a method to generate two random numbers that are different from each other.
Here comes the five move operations that the heuristic will be using.

#### swapNodesInRoute
Swap two nodes in a route. Here, both the route and the two nodes are randomly chosen.
In this move we select a random route among all routes and then we swap two nodes.
Remember to avoid no-operation, we need to select two nodes that are different from each other.
Example of the move, random node indices are 1 and 7, which are shown in bold.
```yaml
Before: hub: 24	nodes: 64,**29**,72,55,71,12,48,**11**
After:  hub: 24	nodes: 64,**11**,72,55,71,12,48,**29**
```
Notice that bold nodes are swapped after the move.

#### swapHubWithNodeInRoute
Swap hub with a randomly chosen node in a route. Here, both the route and the node are randomly chosen.
In this move we select a random route among all routes and then we replace the hub with a random node.
There it is *important* to update the hub in the remaining routes of the initial hub.

Example of the move, random node index is are 10, which is shown in bold.

Before:
hub : **49**	
  hub: 49 nodes : 11,20,26,78,30,0,41,63,44,34,**8**,47,14,31,2,69,50
  hub: 49 nodes: 18,54,51,27,37
After:
hub : **8**
  hub: 8 nodes: 11,20,26,78,30,0,41,63,44,34,**49**,47,14,31,2,69,50

Notice that bold node is replaced with the hub in the first route. Notice also that hub of the second route is updated. Nodes of the second route remain intact.