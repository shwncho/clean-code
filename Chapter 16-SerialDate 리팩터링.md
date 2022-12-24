# 16장 SerialDate 리팩터링

- 첫째, 돌려보자
    - stringToMonthCode 수정
    
    ```java
    if ((result < 1) || (result > 12)) {
    result = -1;
    	for ( int i=0; i< monthNames.length; i++) {
    		if (s.equalsIgnoreCase(shortMonthNames[i])) {
    			result = i + 1;
    			break;
    		}
    		if ( s.equalsIgnoreCase(monthNames[i])) {
    			result = i+1;
    			break;
    		}
    	}
    }
    ```
    
    - getFollowingdayOfWeek 및 알고리즘 수정
    
    ```java
    if (baseDOW >= targetWeekday)
    
    int delta = targetDOW - base.getDayOfWeek();
    int positiveDelta = delta + 7;
    int adjust = positiveDelta % 7;
    if (adjust > 3)
    		adjust -=7;
    
    return SerialDate.addDays(adjust, base);
    ```
    

- 둘째, 고쳐보자

```java
public abstract class DayDate implements COmparable, Serializable {
	public static enum Month {
		JANUARY(1),
		FEBRUARY(2),
		MARCH(3),
		APRIL(4),
		MAY(5),
		JUNE(6),
		JULY(7),
		AUGUST(8),
		SEPTEMBER(9),
		OCTOBER(10),
		NOVEMBER(11),
		DECEMBER(12);

		Month(int index) {
			this.index = index;
		}

		public static Month make(int monthIndex) {
			for (Month m : Month.values()) {
				if (m.index ==monthIndex)
					return m;
				}

				throw new IllegalArgumentException("Invalid month index " + monthIndex);
			}
			public final int index;
		}
	}
```

```java
public abstract class DayDateFactory {
	private static DayDateFactory factory = new SpreadsheetDateFactory();
	public static void setInstance(DayDateFactory factory) {
		DayDateFactory.factory = factory;
	}

	protected abstract DayDate_makeDate(int ordinal);
	protected abstract DayDate_makeDate(int day, DayDate.Month month, int year);
	protected abstract DayDate_makeDate(int day, int month, int year);
	protected abstract DayDate_makeDate(java.util.Date date);
	protected abstract int_getMinimumYear();
	protected abstract int_getMaximumYear();

	public static DayDate makeDate(int ordinal) {
		return factory._makeDate(ordinal);
	}

	public static DayDate_makeDate(int day, DayDate.Month month, int year) {
		return factory._makeDate(day, month, year);
	}

	public static DayDate_makeDate(int day, int month, int year) {
		return factory._makeDate(day, month, year);
	}

	public static DayDate makeDate(java.util.Date date) {
		return factory._makeDate(date);
	}

	public static int getMinimumYear() {
		return factory._getMinimumYear();
	}

	public static int getMaximumYear() {
		return factory._getMaximumYear() ;
	}
}
```

```java
public class SpreadsheetDateFactory extends DayDateFactory {
	public DayDate_makeDate(int ordinal) {
		return new SpreadsheetDate(ordinal);
	}

	public DayDate_makeDate(int day, int DayDate.Month month, int year) {
		return new SpreadsheetDate(day, month, year);
	}

	public DayDate_makeDate(int day, int month, int year) {
		return new SpreadsheetDate(day, month, year);
	}

	public DayDate _makeDate(Date date) {
		final GregorianCalendar calendar = new GregorianCalendar();
		calendar.setTime(date);
		return new SpreadsheetDate(
			calendar.get(Calendar.DATE),
			DayDate.Month.make(calendar.get(Calendar.MONTH) +1),
			calendar.get(Calendar.YEAR));
	}

	protected int _getMinimumYear() {
		return SpreadsheedDate.MINIMUM_YEAR_SUPPORTED;
	}

	protected int _getMaximumYear() {
		return SpreadsheetDate.MAXIMUM_YEAR_SUPPORTED;
	}
}
```

```java
public enum WeekInMonth {
	FIRST(1), SECOND(2), THIRD(3), FOURTH(4), LAST(0);
	public final int index;

	WeekInMonth(int index) {
		this.index = index;
	}
}
```

```java
public enum Day {
	MONDAY(Calendar.MONDAY),
	TUESDAY(Calendar.TUESDAY),
	WEDNESDAY(Calendar.WEDNESDAY),
	THURSDAY(Calendar.THURSDAY),
	FRIDAY(Calendar.FRIDAY),
	SATURDAY(Calendar.SATURDAY),
	SUNDAY(Calendar.SUNDAY);

	public final int index;

	private static DateFormatSymbols dateSymbols = new DateFormatSymbols();

	Day(int day) {
		index = day;
	}

	public static Day make(int index) throws IllegalArgumentException {
		for (Day d : Day.values())
			if (d.index == index)
				return d;
		throw new IllegalArgumentException(
			String.format("Illegal day index: %d.", index));
	}

	public static Day parse(String s) throws IllegalArgumentException {
		String[] shortWeekdayNames =
			dateSymbols.getShortWeekdays();
		String[] weekDayNames =
			dateSymbols.getWeekdays();
		
		s=s.trim();
		for (Day day : Day.values()) {
			if (s.equalsIgnoreCase(shortWeekdayNames[day.index]) ||
					s.equalsIgnoreCase(weekDayNames[day.index])) {
			return day;
			}
		}
		thorw new IllegalArgumentException(
			String.format("%s is not a valid weekday string", s));
	}

	public String toString() {
		return dateSymbols.getWeekdays()[index];
	}
}
```

```java
public static String[] getMonthNames() {
	return dateFormatSymbols.getMonths();
}

public int quarter() {
	return 1 + (index-1)/3;
}

public String toString() {
	return dateFormatSymbols.getMonths()[index - 1];
}

public String toShortString() {
	return dateFormatSymbols.getShortMOnths()[index - 1];
}

public static Month parse(String s) {
	s = s.trim();
	for (Month m : Month.values())
		if (m.matches(s))
			return m;
	try{
		return make(Integer.parseInt(s));
	}
	catch (NumberFormatException e) {}
	throw new IllegalArgumentException("Invalid month" + s);
}

private static boolean isLeapYear(int year) {
	boolean fourth = year % 4 == 0;
	boolean hundredth = year % 100 ==0;
	boolean fourHundredth = year % 400 ==0;
	return fourth && (!hundredth || fourHundredth);
}

public static int lastDayOfMonth(Month month, int year) {
	if (month == Month.FEBRUARY && isLeapYear(year))
		return month.lastDay() + 1;
	else
		return month.lastDay();
}
	
public DayDate addDays(int days) {
	return DayDateFactory.makeDate(toOrdinal() + days);
}

public DayDate addMonths(int months) {
	int thisMonthAsOrdinal = 12 * getYear() + getMonth().index - 1;
	int resultMonthAsOrdinal = thisMonthAsOrdinal + months;
	int resultYear = resultMonthAsOrdinal / 12;
	Month resultMonth = Month.make(resultMonthAsOrdinal % 12 + 1);
}

public DayDate plusYears(int years) {
	int resultYear = getYear() + years;
	int lastDayOfMonthInResultYear = lastDayOfMonth(getMonth(), resultYear);
	int resultDay = Math.min(getDayOfMonth(), lastDayOfMonthInResultYear);
	return DayDateFactory.makeDate(resultDay, getMonth(), resultYear);
}

public DayDate getPreviousDayOfWeek(Day targetDayOfWeek) {
    int offsetToTarget = targetDayOfWeek.index - getDayOfWeek().index;
    if (offsetToTarget >= 0)
        offsetToTarget -= 7;
    return plusDays(offsetToTarget);
}

public DayDate getFollowingDayOfWeek(Day targetDayOfWeek) {
    int offsetToTarget = targetDayOfweek.index - getDayOfWeek().index;
    if (offsetToTarget <= 0)
        offsetToTarget += 7;
    return plusDays(offsetToTarget);
}

public DayDate getNearestDayOfWeek(final Day targetDay) {
    int offsetToThisWeeksTarget = targetDay.index - getDayOfWeek().index;
    int offsetToFutureTarget = (offsetToThisWeeksTarget + 7) % 7;
    int offsetToPreviousTarget = offsetToFutureTarget - 7;
    if (offsetToFutureTarget > 3)
        return plusDays(offsetToPreviousTarget);
    else
        return plusDays(offsetToFutureTarget);
}

public DayDate getEndOfMonth() {
    Month month = getMonth();
    int year = getYear();
    int lastDay = lastDayOfMonth(month, year);
    return DayDateFactory.makeDate(lastDay, month, year);
}

public Day getDayOfWeek() {
    Day startingDay = getDayOfWeekForOrdinalZero(); // 물리적 의존성
    int startingOffset = startingDay.index - Day.SUNDAY.index;
    return Day.make((getOrdinalDay() + startingOffset) % 7 + 1);
}

public enum DateInterval {
    OPEN {
        public boolean isIn(int d, int left, int right) {
            return d > left && d < right;
        }
    },
    CLOSED_LEFT {
        public boolean isIn(int d, int left, int right) {
            return d >= left && d < right;
        }
    },
    CLOSED_RIGHT {
        public boolean isIn(int d, int left, int right) {
            return d > left && d <= right;
        }
    },
    CLOSED {
        public boolean isIn(int d, int left, int right) {
            return d >= left && d <= right;
        }
    };

    public abstract boolean isIn(int d, int left, int right);
}

public boolean isInRange(DayDate di, DayDate d2, DateInterval interval) {
    int left = Math.min(d1.getOrdinalDay(), d2.getOrdinalDay());
    int right = Math.max(d1.getOrdinalDay(), d2.getOrdinalDay());
    return interval.isIn(getOrdinalDay(), left, right);
}
```

&nbsp;
  
- 결론
    - 체크아웃한 코드보다 좀 더 깨끗한 코드를 체크인 했다.
    - 테스트 커버리지 증가
    - 버그 개선
    - 코드 크기 개선