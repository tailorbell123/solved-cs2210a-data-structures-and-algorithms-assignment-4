Download Link: https://assignmentchef.com/product/solved-cs2210a-data-structures-and-algorithms-assignment-4
<br>
<strong>Moving Figures</strong>

<h1>1           Overview</h1>

In this assignment you will write code for manipulating figures, where a figure is defined as a set of points or pixels. We are interested in displaying the figures and moving them around, detecting collisions when they occur.

The program that displays the figures will receive as input a file containing a set of figures. The figures will be rendered on a window and the user will move some them around using the keyboard. Figures cannot overlap, so your program will allow a figure to move only when its movement would not cause it to overlap with another figure or with the borders of the window. For this assignment, there will be five kinds of figures:

<ul>

 <li>fixed figures, which cannot move</li>

 <li>figures that can be moved by the user</li>

 <li>figures that are moved by the computer</li>

 <li>target figures, that disappear when the user-controlled figures run into them.</li>

</ul>

These figures will be part of a “pac-man”-like game. Figures moved by the computer will chase the user controlled figures. These latter in turn will try to get rid of the target figures. Fixed figures constrain the movement of the mobile ones.

We will provide code for reading the input file, for displaying the figures and for reading the user input. You will have to write code for storing the figures and for detecting overlaps bewteen figures.

<h1>2           The Figures</h1>

As stated above, each figure consists of a set of pixels. Each pixel is defined by 3 values <em>x</em>, <em>y</em>, and <em>c</em>; (<em>x,y</em>) are the coordinates of the pixel and <em>c </em>is its color. We will think that each figure <em>f </em>is enclosed in a rectangle <em>r<sub>f </sub></em>(so all the pixels are inside this rectangle and no smaller rectangle would contain all the pixels; see Fig. 1 below). The width and height of this rectangle are the width and height of the figure. To determine the position where a figure should be displayed, we need to give the coordinates (<em>u<sub>x</sub>,u<sub>y</sub></em>) of the upper-left corner of its enclosing rectangle; (<em>u<sub>x</sub>,u<sub>y</sub></em>) is called the <em>offset </em>of the figure.

For specifying coordinates, we assume that the upper-left corner of the window <em>ω </em>where the figures are displayed has coordinates (0<em>,</em>0). The coordinates of the lower-right corner of <em>ω </em>are (<em>W,H</em>), where <em>W </em>is the width and <em>H </em>is the height of <em>ω</em>.

Each figure will have an identifier; this is just an integer number that is used to distinguish a figure from another, as two figures might contain the same set of pixels (but not in the same position).

The pixels of a figure <em>f </em>will be stored in a binary search tree. Each node in the tree stores a data item (position,color), where position = (<em>x,y</em>) contains the coordinates of the pixel <strong>relative </strong>to the upperleft corner of the rectangle <em>r<sub>f </sub></em>enclosing the figure. For example, the coordinates of the black dot in Fig. 1 below are (20<em>,</em>10), so this black dot corresponds to the pixel ((20<em>,</em>10)<em>,</em>black). As shown in Fig. 1, the offset of figure <em>f</em><sub>1 </sub>is (40<em>,</em>25), so when rendering <em>f</em><sub>1 </sub>inside the window the actual position of the black dot is (20 + 40<em>,</em>10 + 25) = (60<em>,</em>35).

Fig. 1 Window <em>ω</em>.

Note that by storing in the tree pixels with coordinates relative to the figure’s enclosing rectangle, the data stored in the tree does not need to change when the figure moves: The only thing that needs to change is the figure’s offset.

For each data item (position,color) stored in the tree, the field position is used as the key. To compare two positions (<em>x,y</em>) and (<em>x</em><sup>′</sup><em>,y</em><sup>′</sup>) we use <em>row order</em>: (<em>x,y</em>) <em>&lt; </em>(<em>x</em><sup>′</sup><em>,y</em><sup>′</sup>) if either

<ul>

 <li><em>y &lt; y</em><sup>′</sup>, or</li>

 <li><em>y </em>= <em>y</em><sup>′ </sup>and <em>x &lt; x</em><sup>′</sup></li>

</ul>

So, for example, (2<em>,</em>3) <em>&lt; </em>(1<em>,</em>4) and (5<em>,</em>3) <em>&lt; </em>(9<em>,</em>3).

<h1>3           Moving Figures</h1>

As stated above, two figures cannot overlap and a figure cannot go outside the window <em>ω</em>. Hence, when the user tries to move a figure, we need to verify that such a movement would not cause the figure to cross the boundaries of the window or to overlap another figure.

A movement can be represented as a pair (<em>d<sub>x</sub>,d<sub>y</sub></em>), where <em>d<sub>x </sub></em>is the distance to move horizontally and <em>d<sub>y </sub></em>is the distance to move vertically. To check whether a movement (<em>d<sub>x</sub>,d<sub>y</sub></em>) on figure <em>f </em>with offset (<em>x<sub>f</sub>,y<sub>f</sub></em>), width <em>w<sub>f </sub></em>and height <em>h<sub>f </sub></em>is valid, we first update the offset of <em>f </em>to (<em>x<sub>f </sub></em>+ <em>d<sub>x</sub>,y<sub>f </sub></em>+ <em>d<sub>y</sub></em>) and then check whether this new position for <em>f </em>would cause an overlap with another figure or with the window’s borders. To do this efficiently we proceed as follows:

<ul>

 <li>Check whether the enclosing rectangle <em>r<sub>f </sub></em>of <em>f </em>crosses the borders of the window <em>ω</em>. For example, to check whether <em>r<sub>f </sub></em>crosses the right border of <em>ω </em>we test if <em>x<sub>f </sub></em>+<em>d<sub>x </sub></em>+<em>w<sub>f </sub></em>≥ <em>W</em>; recall that <em>W </em>is the width of <em>ω</em>.</li>

 <li>If <em>r<sub>f </sub></em>does not cross the borders of <em>ω</em>, then we check whether <em>r<sub>f </sub></em>intersects the enclosing rectangle <em>r<sub>f</sub></em>′ of another figure <em>f</em><sup>′</sup>. If there is no such intersection then <em>f </em>does not intersect other figures or the window’s borders, so the movement (<em>d<sub>x</sub>,d<sub>y</sub></em>) is valid.</li>

 <li>On the other hand, if <em>r<sub>f </sub></em>intersects the enclosing rectangles of some set <em>S </em>of figures, then for each figure <em>f</em><sup>′ </sup>∈ <em>S </em>we check whether <em>f </em>and <em>f</em><sup>′ </sup>overlap; if so, then this movement should not be allowed.</li>

</ul>

Note that for <em>f </em>and <em>f</em><sup>′ </sup>to overlap, <em>f </em>must have at least one pixel ((<em>x,y</em>)<em>,c</em>) and <em>f</em><sup>′ </sup>must have a pixel ((<em>x</em><sup>′</sup><em>,y</em><sup>′</sup>)<em>,c</em><sup>′</sup>) that would be displayed at precisely the same position on <em>ω</em>, or in other words, <em>x </em>+ <em>x<sub>f </sub></em>= <em>x</em><sup>′ </sup>+ <em>x<sub>f</sub></em><sup>′ </sup>and <em>y </em>+ <em>y<sub>f </sub></em>= <em>y</em><sup>′ </sup>+ <em>y<sub>f</sub></em><sup>′</sup>, where (<em>x<sub>f</sub></em><sup>′</sup><em>,y<sub>f</sub></em><sup>′</sup>) is the offset of <em>f</em><sup>′</sup>. Note that if these pixels exist then <em>x </em>+ <em>x<sub>f </sub></em>− <em>x<sub>f</sub></em><sup>′ </sup>= <em>x</em><sup>′ </sup>and <em>y </em>+ <em>y<sub>f </sub></em>− <em>y<sub>f</sub></em><sup>′ </sup>= <em>y</em><sup>′</sup>. Therefore, to test whether <em>f </em>and <em>f</em><sup>′ </sup>overlap we can use the following algorithm:

<strong>For </strong>each data item ((<em>x,y</em>)<em>,c</em>) stored in the binary search tree <em>t<sub>f </sub></em>storing the pixels of <em>f </em><strong>do </strong>(1) <strong>if </strong>in the tree <em>t<sub>f</sub></em>′ storing the pixels of <em>f</em><sup>′ </sup>there is a data item ((<em>x</em><sup>′</sup><em>,y</em><sup>′</sup>)<em>,c</em><sup>′</sup>) with key

(<em>x</em><sup>′</sup><em>,y</em><sup>′</sup>) = (<em>x </em>+ <em>x<sub>f </sub></em>− <em>x<sub>f</sub></em>′<em>,y </em>+ <em>y<sub>f </sub></em>− <em>y<sub>f</sub></em>′), <strong>then </strong>the figures overlap.

<strong>if </strong>Condition (1) is not satisfied, then the figures do not overlap.

In the <strong>for </strong>loop above, to consider all the data items ((<em>x,y</em>)<em>,c</em>) stored in the nodes of the tree <em>t<sub>f </sub></em>we can use the binary search tree operations smallest() and successor().

<h1>4           Classes to Implement</h1>

You are to implement at least five Java classes: Position, DictEntry, BinarySerachTree, BSTException, and Figure. You can implement more classes if you need to. <strong>You must write all the code yourself</strong>. You <strong>cannot </strong>use code from the textbook, the Internet, or any other sources: however, you may implement the algorithms discussed in class.

<h2>4.1         Position</h2>

This class represents the position (<em>x,y</em>) of a pixel. For this class you must implement all and only the following public methods:

<ul>

 <li>public Position(int x, int y): A constructor which returns a new Position with the specified coordinates.</li>

 <li>public int getX(): Returns the <em>x </em>coordinate of <strong>this </strong>Position.</li>

 <li>public int getY(): Returns the <em>y </em>coordinate of <strong>this </strong>Position.</li>

 <li>public int compareTo (Position p): Compares <strong>this</strong>Position with <em>p </em>using row order (defined above):

  <ul>

   <li>if <strong>this </strong><em>&gt; </em>p return 1;</li>

   <li>if <strong>this </strong>= p return 0;</li>

   <li>if <strong>this </strong><em>&lt; </em>p return -1.</li>

  </ul></li>

</ul>

You can implement any other methods that you want to in this class, but they must be declared as private methods (i.e. not accessible to other classes).

<h2>4.2         DictEntry</h2>

This class represents a data item stored in the binary search tree. Each data item consists of two parts: a Position and an int color. For this class, you must implement all and only the following public methods:

<ul>

 <li>public DictEntry(Position p, int color): A constructor which returns a new DictEntry with the specified coordinates and color. Position p is the key for the DictEntry.</li>

 <li>public Position getPosition(): Returns the Position in the DictEntry.</li>

 <li>public int getColor(): Returns the color in the DictEntry.</li>

</ul>

You can implement any other methods that you want to in this class, but they must be declared as private methods.

<h2>4.3         BinarySearchTree</h2>

This class implements an ordered dictionary using a binary search tree. Each node of the tree will store a DictEntry object; the attribute Position of the DictEntry will be its key. In your binary search tree <strong>only the internal nodes will store information</strong>. The leaves are nodes (<strong>not </strong>null) that do not store any data.

The constructor for the BinarySearchTree class must be of the form public BinarySearchTree()

This will create a tree whose root is a leaf node. Beside the constructor, the only other public methods in this class are specified in the BinarySearchTreeADT interface:

<ul>

 <li>public DictEntry find (Position key): Returns the DictEntry storing the given key, if the key is stored in the tree. Returns null otherwise.</li>

 <li>public void insert (DictEntry data) throws BSTException: Inserts the given data in the tree if no data item with the same key is already there. If a node already stores the same key, the algorithm throws a BSTException.</li>

 <li>public void remove (Position key) throws BSTException: Removes the data item with the given key, if the key is stored in the tree. Throws a BSTException otherwise.</li>

 <li>public DictEntry successor (Position key): Returns the DictEntry with the smallest key larger than the given one (note that the tree does not need to store a node with the given key). Returns null if the given key has no successor.</li>

 <li>public DictEntry predecessor (Position key): Returns the DictEntry with the largest key smaller than the given one (note that the tree does not need to store a node with the given key). Returns null if the given key has no predecessor.</li>

 <li>public DictEntry smallest(): Returns the DictEntry with the smallest key. Returns null if the tree does not contain any data.</li>

 <li>public DictEntry largest(): Returns the DictEntry with the largest key. Returns null if the tree does not contain any data.</li>

</ul>

You can download BinarySearchTreeADT.java from the course’s website. To implement this interface, you need to declare your BinarySearchTree class as follows:

public class BinarySearchTree implements BinarySearchTreeADT

You can implement any other methods that you want to in this class, but they must be declared as private methods.

<h2>4.4         BSTException</h2>

This is just the class implementing the class of exceptions thrown by the insert and remove methods of BinarySearchTree. See the class notes on exceptions.

<h2>4.5         Figure</h2>

The constructor for this class must be of the form

public Figure (int id, int width, int height, int type, Position pos);

where id is the figure’s identifier, width and height are the width and height of the enclosing rectangle for <strong>this </strong>figure, pos is the offset of the figure and type is its type. The figure types are

<ul>

 <li>0: fixed figure</li>

 <li>1: figure moved by the user 2: figure moved by the computer</li>

 <li>3: target figure.</li>

</ul>

Beside the constructor, the only other public methods in this class are specified in the FigureADT interface:

<ul>

 <li>public void setType (int type): Sets type of figure to the specified value.</li>

 <li>public int getWidth (): Returns the width of the enclosing rectangle for <strong>this </strong></li>

 <li>public int getHeight(): Returns the height of the enclosing rectangle for <strong>this </strong></li>

 <li>public int getType (): Returns the type of <strong>this </strong></li>

 <li>public int getId(): Returns the id of <strong>this </strong></li>

 <li>public Position getOffset(): Returns the offset of <strong>this </strong></li>

 <li>public void setOffset(Position value): Changes the offset of <strong>this </strong>figure to the specified value</li>

 <li>public void addPixel(int x, int y, int color) throws BSTException: Creates a</li>

</ul>

DictEntry to represent the given pixel and inserts it into the binary search tree associated with <strong>this </strong>figure. Throws a BSTException if an error occurs when inserting the pixel into the tree.

<ul>

 <li>public boolean intersects (Figure fig): Returns true if <strong>this </strong>figure intersects the one specified in the parameter. It returns false</li>

</ul>

You can download FigureADT.java from the course’s website. To implement this interface, you need to declare your Figure class as follows:

public class Figure implements FigureADT

You can implement any other methods that you want to in this class, but they must be declared as private methods.

<h1>5           Classes Provided and Running the Program</h1>

The input to the program will be a file containing the descriptions of the figures to be displayed. Each line of the input file contains 4 values: x y type file

where (x,y) is the offset of the figure, type is the type of the figure (these 3 values are integer), and file is the name of an image file in .jpg, .bmp, or any other image format understood by java. You will be given code for reading the input file. We specify the format of the input file just in case you want to create your own inputs.

From the course’s website you can download the following classes: Board.java, Gui.java, MoveFigure.java, and Show.java. The main method is in class Show.java. To execute the program, on a command window you will enter the command

java Show <em>inputFile </em>where <em>inputFile </em>is the name of the file containing the input for the program.

<h1>6           Testing your Program</h1>

We will run a test program called TestBST to make sure that your implementation of the BinarySearchTree class is as specified above. We will supply you with a copy of TestBST to test your implementation. We will also run other tests on your software to check whether it works properly.