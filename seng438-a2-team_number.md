**SENG 438 - Software Testing, Reliability, and Quality**

**Lab. Report \#2 – Requirements-Based Test Generation**

| Group: Group 14|     |
| -------------- | --- |
| Student Names: |     |
| Eshi Akalumhe  |     |
| Emiko Emiko    |     |
| Abhyudai Singh |     |
| Marco Truong   |     |

# 1 Introduction

The goal of this assignment is to teach students the basics of automated unit testing, with a focus on testing based on the requirements for each unit. Upon completion of this lab, we now have a reasonable understanding of unit testing based on unit requirements using the JUnit framework.

# 2 Detailed description of unit test strategy

Starting with **org.jfree.data.Range*, we plan to test the following methods:
- **getCentralValue()**: Returns the central (or median) value for the range.
- **getLowerBound()**: Returns the lower bound for the range.
- **getUpperBound()**: Returns the upper bound for the range.
- **getLength()**: Returns the length of the range.
- **contains()**: Returns true if the specified value is within the range and false otherwise.

We'll create three Range objects, one with a negative and positive number, one with two positive numbers, and one with two negative numbers. These three objects cover all the distinct partitions and boundary values 
needed to test the methods above. So we'll have three test cases for all five methods, with each test case using one of the three Range objects to call them.

Then we'll make a test suite for all five methods in **org.jfree.data.DataUtilities*:
- **calculateColumnTotal(Values2D data, int column)**: Returns the sum of the values in one column of the supplied data table.
- **calculateRowTotal(Values2D data, int row)**: Returns the sum of the values in one row of the supplied data table.
- **createNumberArray(double[] data)**: Constructs an array of Number objects from an array of double primitives.
- **createNumberArray2D(double[][] data)**: Constructs an array of arrays of Number objects from a corresponding structure containing double primitives.
- **getCumulativePercentages(KeyedValues data)**: Returns a KeyedValues instance that contains the cumulative percentage values for the data in another KeyedValues instance.

Since some of these methods use the Values2D and KeyedValues interfaces, we'll use the Mockery class to pass simulated objects to the methods being tested. That is, we'll use Mock objects to "mock" Values2D and KeyedValues objects, which will then be passed as parameters to our methods. (Mock Values2D objects for calculateColumnTotal and calculateRowTotal and mock KeyedValues object for getCumulativePercentages)

# 3 Test cases developed

**org.jfree.data.Range()**

package org.jfree.data.test; 

import static org.junit.Assert.*; import org.jfree.data.Range; import org.junit.*;

public class RangeTest {
    private Range posnegrange;
    private Range posrange;
    private Range negrange;
    
    @BeforeClass public static void setUpBeforeClass() throws Exception {
    }


    @Before
    public void setUp() throws Exception { 
        posnegrange = new Range(-1, 1);
        
        posrange = new Range(5, 10);
        
        negrange = new Range(-10,-5);
        
    }


    @Test
    public void centralValueShouldBeZero() {
        assertEquals("The central value of -1 and 1 should be 0", 0, posnegrange.getCentralValue(), .000000001d);
    }
    @Test
    public void centralValueShouldBeSevenPointFive() {
        assertEquals("The central value of 5 and 10 should be 0", 7.5, posrange.getCentralValue(), .000000001d);
    }
    @Test
    public void centralValueShouldBeNegSevenPointFive() {
        assertEquals("The central value of -10 and 5 should be 0", -7.5, negrange.getCentralValue(), .000000001d);
    }

    
    
    @Test
    public void lowerValueShouldBeNegOne() {
        assertEquals(-1, posnegrange.getLowerBound(), 0.0001);
    }
    @Test
    public void lowerValueShouldBeFive() {
        assertEquals(5, posrange.getLowerBound(), 0.0001);
    }
    @Test
    public void lowerValueShouldBeNegTen() {
        assertEquals(-10, negrange.getLowerBound(), 0.0001);
    }
    
    
    
    
    @Test
    public void upperValueShouldBeOne() {
        assertEquals(1, posnegrange.getUpperBound(), 0.0001);
    }
    @Test
    public void upperValueShouldBeTen() {
        assertEquals(10, posrange.getUpperBound(), 0.0001);
    }
    @Test
    public void upperValueShouldBeNegFive() {
        assertEquals(-5, negrange.getUpperBound(), 0.0001);
    }
    
    
    @Test
    public void legnthShouldBeTwo() {
        assertEquals(2, posnegrange.getLength(),0.0001);
    }
    @Test
    public void legnthShouldBeFive() {
        assertEquals(5, posrange.getLength(),0.0001);
        assertEquals(5, negrange.getLength(),0.0001);
    }
    
    
 
    @Test
    public void containszero() {
        assertTrue(posnegrange.contains(0));
    }
    @Test
    public void containsSeven() {
        assertTrue(posrange.contains(7));
    }
    @Test
    public void containsNegSeven() {
        assertTrue(negrange.contains(-7));
    }
    
    @After
    public void tearDown() throws Exception {
    }

    @AfterClass
    public static void tearDownAfterClass() throws Exception {
    }
}

From the test suite above, we can see that:

- centralValueShouldBeZero(), centralValueShouldBeSevenPointFive(), and centralValueShouldBeNegSevenPointFive() make up one equivalence class, since they all test the same method (getCentralValue()).
- lowerValueShouldBeNegOne(), lowerValueShouldBeFive(), and lowerValueShouldBeNegTen() make up the equivalence class for **getLowerBound()*.
- upperValueShouldBeOne(), upperValueShouldBeTen(), and upperValueShouldBeNegFive() make up the equivalence class for **getUpperBound()*.
- legnthShouldBeTwo(), and legnthShouldBeFive() make up the equivalence class for **getLength()*.



**org.jfree.data.DataUtilities**
package org.jfree.data.test;

import static org.junit.Assert.*;

import org.jfree.data.DataUtilities;
import org.jfree.data.Values2D;
import org.jfree.data.KeyedValues;
import org.jmock.Expectations;
import org.jmock.Mockery;
import org.junit.Test;

public class DataUtilitiesTest extends DataUtilities {
	
	@Test
	public void calculateColumnTotalForTwoValues() {
	    // setup
	    Mockery mockingContext = new Mockery();
	    final Values2D values = mockingContext.mock(Values2D.class);
	    mockingContext.checking(new Expectations() {
	        {
	            one(values).getRowCount();
	            will(returnValue(2));
	            one(values).getValue(0, 0);
	            will(returnValue(7.5));
	            one(values).getValue(1, 0);
	            will(returnValue(2.5));
	        }
	    });
	    double result = DataUtilities.calculateColumnTotal(values, 0);
	    // verify
	    assertEquals(result, 10.0, .000000001d);
	    // tear-down: NONE in this test method
	}
	
	@Test
	public void calculateColumnTotalForFiveValues() {
	    // setup
	    Mockery mockingContext = new Mockery();
	    final Values2D values = mockingContext.mock(Values2D.class);
	    mockingContext.checking(new Expectations() {
	        {
	            one(values).getRowCount();
	            will(returnValue(5));
	            one(values).getValue(0, 0);
	            will(returnValue(7.5));
	            one(values).getValue(1, 0);
	            will(returnValue(2.5));
	            one(values).getValue(2, 0);
	            will(returnValue(2.5));
	            one(values).getValue(3, 0);
	            will(returnValue(2.5));
	            one(values).getValue(4, 0);
	            will(returnValue(7.5));
	        }
	    });
	    double result = DataUtilities.calculateColumnTotal(values, 0);
	    // verify
	    assertEquals(result, 22.5, .000000001d);
	    // tear-down: NONE in this test method
	}
	
	@Test
	public void calculateColumnTotalOfEmptyTable() {
	 // setup
	 Mockery mockingContext = new Mockery();
	 final Values2D values = mockingContext.mock(Values2D.class);
	    mockingContext.checking(new Expectations() {
	        {
	            one(values).getRowCount();
	            will(returnValue(0));
	            one(values).getColumnCount();
	            will(returnValue(0));
	        }
	    });
	    double result = DataUtilities.calculateColumnTotal(values, 0);
	    // verify
	    assertEquals(result, 0.0, .000000001d);
	    // tear-down: NONE in this test method
	}
	
	@Test
	public void calculateColumnTotalWithOutOfBoundIndex() {
	    // setup
	    Mockery mockingContext = new Mockery();
	    final Values2D values = mockingContext.mock(Values2D.class);
	    mockingContext.checking(new Expectations() {
	        {
	            one(values).getRowCount();
	            will(returnValue(2));
	            one(values).getValue(0, 0);
	            will(returnValue(7.5));
	            one(values).getValue(1, 0);
	            will(returnValue(2.5));
	        }
	    });
	    double result = DataUtilities.calculateColumnTotal(values, 3);
	    // verify
	    assertEquals(result, 0.0, .000000001d);
	    // tear-down: NONE in this test method
	}
	
	@Test(expected = IllegalArgumentException.class)
	public void calculateColumnTotalWithInvalidDataObject() {
		DataUtilities.calculateColumnTotal(null, 1);
	}
	
	// calculateRowTotal
	@Test
	public void calculateRowTotalForTwoValues() {
	    // setup
	    Mockery mockingContext = new Mockery();
	    final Values2D values = mockingContext.mock(Values2D.class);
	    mockingContext.checking(new Expectations() {
	        {
	            one(values).getColumnCount();
	            will(returnValue(2));
	            one(values).getValue(0, 0);
	            will(returnValue(7.5));
	            one(values).getValue(0, 1);
	            will(returnValue(2.5));
	        }
	    });
	    double result = DataUtilities.calculateRowTotal(values, 0);
	    // verify
	    assertEquals(result, 10.0, .000000001d);
	    // tear-down: NONE in this test method
	}
	
	@Test
	public void calculateRowTotalForFiveValues() {
	    // setup
	    Mockery mockingContext = new Mockery();
	    final Values2D values = mockingContext.mock(Values2D.class);
	    mockingContext.checking(new Expectations() {
	        {
	            one(values).getColumnCount();
	            will(returnValue(5));
	            one(values).getValue(0, 0);
	            will(returnValue(7.5));
	            one(values).getValue(0, 1);
	            will(returnValue(2.5));
	            one(values).getValue(0, 2);
	            will(returnValue(2.5));
	            one(values).getValue(0, 3);
	            will(returnValue(2.5));
	            one(values).getValue(0, 4);
	            will(returnValue(7.5));
	        }
	    });
	    double result = DataUtilities.calculateRowTotal(values, 0);
	    // verify
	    assertEquals(result, 22.5, .000000001d);
	    // tear-down: NONE in this test method
	}
	
	@Test
	public void calculateRowTotalOfEmptyTable() {
	 // setup
	 Mockery mockingContext = new Mockery();
	 final Values2D values = mockingContext.mock(Values2D.class);
	    mockingContext.checking(new Expectations() {
	        {
	            one(values).getRowCount();
	            will(returnValue(0));
	            one(values).getColumnCount();
	            will(returnValue(0));
	        }
	    });
	    double result = DataUtilities.calculateColumnTotal(values, 0);
	    // verify
	    assertEquals(result, 0.0, .000000001d);
	    // tear-down: NONE in this test method
	}
	
	@Test
	public void calculateRowTotalWithOutOfBoundIndex() {
	    // setup
	    Mockery mockingContext = new Mockery();
	    final Values2D values = mockingContext.mock(Values2D.class);
	    mockingContext.checking(new Expectations() {
	        {
	            one(values).getColumnCount();
	            will(returnValue(2));
	            one(values).getValue(0, 0);
	            will(returnValue(7.5));
	            one(values).getValue(0, 1);
	            will(returnValue(2.5));
	        }
	    });
	    double result = DataUtilities.calculateColumnTotal(values, 3);
	    // verify
	    assertEquals(result, 0.0, .000000001d);
	    // tear-down: NONE in this test method
	}
	
	@Test(expected = IllegalArgumentException.class)
	public void calculateRowTotalWithInvalidDataObject() {
		DataUtilities.calculateRowTotal(null, 1);
	}
	
	// createNumberArray
	@Test
	public void testCreateNumberArrayNormalCase() {
	 double[] data = {1.0, 2.0, 3.0};
	    Number[] result = DataUtilities.createNumberArray(data);
	    System.out.println(result);
	    assertArrayEquals("Array should contain Number equivalents of double values",
	                         new Number[]{1.0, 2.0, 3.0}, result);
	}
	
	@Test
	public void testCreateNumberArrayEmptyArray() {
	    double[] data = {};
	    Number[] result = DataUtilities.createNumberArray(data);
	    assertEquals("Resulting array should be empty", 0, result.length);
	}
	
	@Test(expected = IllegalArgumentException.class)
	public void testCreateNumberArrayNullCase() {
	    DataUtilities.createNumberArray(null);
	}
	
	@Test
	public void testCreateNumberArraySingleElement() {
	    double[] data = {42.0};
	    Number[] result = DataUtilities.createNumberArray(data);
	    assertArrayEquals("Array should contain a single Number equivalent of double value",
	                      new Number[]{42.0}, result);
	}
	
	@Test
	public void testCreateNumberArrayLargeArray() {
	    double[] data = new double[1000];
	    for(int i = 0; i < data.length; i++) {
	        data[i] = i;
	    }
	    Number[] result = DataUtilities.createNumberArray(data);
	    Number[] expected = new Number[1000];
	    for(int i = 0; i < expected.length; i++) {
	        expected[i] = i;
	    }
	    assertArrayEquals("Large arrays should be equal", expected, result);
	}
	
	@Test
	public void testCreateNumberArraySpecialValues() {
	    double[] data = {Double.NaN, Double.POSITIVE_INFINITY, Double.NEGATIVE_INFINITY};
	    Number[] result = DataUtilities.createNumberArray(data);
	    Number[] expected = {Double.NaN, Double.POSITIVE_INFINITY, Double.NEGATIVE_INFINITY};
	    for (int i = 0; i < expected.length; i++) {
	        assertEquals("Special value should be equal",
	            expected[i].doubleValue(), result[i].doubleValue(), 0.0);
	    }
	}

	// createNumberArray2D() 
	@Test
    public void testCreateNumberArray2DNormalCase() {
        double[][] data = {{1.0, 2.0}, {3.0, 4.0}};
        Number[][] result = DataUtilities.createNumberArray2D(data);
        assertArrayEquals("Array should contain Number equivalents of double values",
                          new Number[][]{{1.0, 2.0}, {3.0, 4.0}}, result);
    }

    @Test
    public void testCreateNumberArray2DEmptyArray() {
        double[][] data = {};
        Number[][] result = DataUtilities.createNumberArray2D(data);
        assertEquals("Resulting array should be empty", 0, result.length);
    }

    @Test
    public void testCreateNumberArray2DSubArrayEmpty() {
        double[][] data = {{}, {1.0, 2.0}};
        Number[][] result = DataUtilities.createNumberArray2D(data);
        assertEquals("First sub-array should be empty", 0, result[0].length);
        assertArrayEquals("Second sub-array should contain Numbers",
                          new Number[]{1.0, 2.0}, result[1]);
    }

    @Test(expected = IllegalArgumentException.class)
    public void testCreateNumberArray2DNullCase() {
        DataUtilities.createNumberArray2D(null);
    }

    @Test
    public void testCreateNumberArray2DSingleElement() {
        double[][] data = {{42.0}};
        Number[][] result = DataUtilities.createNumberArray2D(data);
        assertArrayEquals("Array should contain a single sub-array with a single Number",
                          new Number[][]{{42.0}}, result);
    }

    @Test
    public void testCreateNumberArray2DVariedLength() {
        double[][] data = {{1.0}, {2.0, 3.0}};
        Number[][] result = DataUtilities.createNumberArray2D(data);
        assertArrayEquals("First sub-array should contain a single Number",
                          new Number[]{1.0}, result[0]);
        assertArrayEquals("Second sub-array should contain two Numbers",
                          new Number[]{2.0, 3.0}, result[1]);
    }

    @Test
    public void testCreateNumberArray2DSpecialValues() {
        double[][] data = {{Double.NaN, Double.POSITIVE_INFINITY}, {Double.NEGATIVE_INFINITY}};
        Number[][] result = DataUtilities.createNumberArray2D(data);
        assertEquals("Special value NaN should be equal",
                     Double.NaN, result[0][0].doubleValue(), 0.0);
        assertEquals("Special value POSITIVE_INFINITY should be equal",
                     Double.POSITIVE_INFINITY, result[0][1].doubleValue(), 0.0);
        assertEquals("Special value NEGATIVE_INFINITY should be equal",
                     Double.NEGATIVE_INFINITY, result[1][0].doubleValue(), 0.0);
    }
    
    // getCumulativePercetages
    
    @Test
    public void testGetCumulativePercentagesNormalCase() {
    	// setup
	    Mockery mockingContext = new Mockery();
	    final KeyedValues values = mockingContext.mock(KeyedValues.class);
	    mockingContext.checking(new Expectations() {
	        {
	            one(values).getKeys();
	            will(returnValue(new int[] {0, 1, 2}));
	            one(values).getValue(0);
	            will(returnValue(5));
	            one(values).getValue(1);
	            will(returnValue(9));
	            one(values).getValue(2);
	            will(returnValue(2));
	        }
	    });

        KeyedValues result = DataUtilities.getCumulativePercentages(values);
        
        // verify
        assertEquals("The first value should be 5/16", 0.3125, result.getValue(0).doubleValue(), 0.0001);
        assertEquals("The second value should be (5+9)/16", 0.875, result.getValue(1).doubleValue(), 0.0001);
        assertEquals("The third value should be 1.0", 1.0, result.getValue(2).doubleValue(), 0.0001);
    }

    @Test
    public void testGetCumulativePercentagesEmptyCase() {
    	// setup
	    Mockery mockingContext = new Mockery();
	    final KeyedValues values = mockingContext.mock(KeyedValues.class);
	    mockingContext.checking(new Expectations() {
	        {
	            one(values).getKeys();
	            will(returnValue(new int[] {}));
	        }
	    });

        KeyedValues result = DataUtilities.getCumulativePercentages(values);

        assertTrue("Resulting KeyedValues should be empty", result.getKeys().isEmpty());
    }

    @Test(expected = IllegalArgumentException.class)
    public void testGetCumulativePercentagesNullCase() {
        DataUtilities.getCumulativePercentages(null);
    }

    @Test
    public void testGetCumulativePercentagesSingleElement() {
    	// setup
	    Mockery mockingContext = new Mockery();
	    final KeyedValues values = mockingContext.mock(KeyedValues.class);
	    mockingContext.checking(new Expectations() {
	        {
	            one(values).getKeys();
	            will(returnValue(new int[] {0}));
	            one(values).getValue(0);
	            will(returnValue(5));        
	        }
	    });

        KeyedValues result = DataUtilities.getCumulativePercentages(values);

        assertEquals("The single value should have a cumulative percentage of 1.0", 1.0, result.getValue(0).doubleValue(), 0.0001);
    }
}


For this test suite we can see that:

- calculateColumnTotalForTwoValues(), calculateColumnTotalForFiveValues(), calculateColumnTotalOfEmptyTable(), calculateColumnTotalWithOutOfBoundIndex(), and calculateColumnTotalWithInvalidDataObject() make up the equivalence class for the **calculateColumnTotal()* method.

- calculateRowTotalForTwoValues(), calculateRowTotalForFiveValues(), calculateRowTotalOfEmptyTable(), calculateRowTotalWithOutOfBoundIndex(), and calculateRowTotalWithInvalidDataObject() make up the equivalence class for the **calculateRowTotal()* method.

- testCreateNumberArrayNormalCase(), testCreateNumberArrayEmptyArray(), testCreateNumberArrayNullCase(), testCreateNumberArraySingleElement(), testCreateNumberArrayLargeArray(), and testCreateNumberArraySpecialValues() make up the equivalence class for the **createNumberArray()* method.

- testCreateNumberArray2DNormalCase(), testCreateNumberArray2DEmptyArray(), testCreateNumberArray2DSubArrayEmpty(), testCreateNumberArray2DNullCase(), and testCreateNumberArray2DSingleElement() make up the equivalence class for the **createNumberArray2D()* method.

- testGetCumulativePercentagesNormalCase(), testGetCumulativePercentagesEmptyCase(), testGetCumulativePercentagesNullCase(), and testGetCumulativePercentagesSingleElement() make up the equivalence class for the **getCumulativePercentages()* method.

As shown above, these test cases cover all the necessary boundary value,invalid input and edge cases needed to perform proper BlackBox testing as mentioned in the lectures.


# 4 How the team work/effort was divided and managed

As the we were given 20 methods, we decided to split the methods into 4 so that each group member is able to practice using JUnit. Members were given freedom to practice as many tests for the methods provided as they would like but were mandated to perform their own sections of testing. As such, we came together to choose the best test case options.


# 5 Difficulties encountered, challenges overcome, and lessons learned

We had difficulities in scheduling and organizing an in-person meeting so we concluded on discussing the assignemnt through chat and remote options. We learned that we should stick to remote options to prevent delay in completion of the assignment due to scheduling conflicts.


# 6 Comments/feedback on the lab itself

The lab was extensive in teaching us how to implement automated unit testing from the creation of Junit tests using eclipse. We were able to put into practice, previous knowledge and newly discovered information on automated unit testing using Junit and create different methods. We were able to explore and apply different concepts of automated testing in real-world scenarios.

