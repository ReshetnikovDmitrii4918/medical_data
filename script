from matplotlib import pyplot
from numpy import median, short, mean, sqrt, var
import os

class Tone(object):
    def __init__(self,left,right,peak):
        self.LeftPoint = left
        self.RightPoint = right
        self.Peak = peak

def module(array, med):
    y = []
    for i in range(len(array)):
        y.append(abs(array[i]-med))
    return y   

# Сравниваем значение с медианным значением
# Если значения отличаются больше чем на 10 процентов, то мы их не меняем, иначе приравниваем их к медианному
def denoise(x,avg,threshold):
    low = avg - threshold
    high = avg+threshold
    if x < high and x > low:
        return avg
    else :
        return x

# KAA: Нормализуем значение. 
def normalize(x,avg):
    shift = x - avg
    if shift >= 0:
        return shift
    else :
        return -shift

def cutOff(x, threshold):
    if x >= threshold:
        return x
    else :
        return 0



#находим левые и правые края по следующим условиям
# для левого края - слева определенное кол-во точек равны медианному, а следующая точка отличается
# для правого края - справа определенное кол-во точек равны медианному, а предыдущая точка отличается
def findEdges(array,avg,threshold):
    LeftEdges = []
    RightEdges = []
    for i in range(threshold,len(array)-threshold):
        if isLeftStraight(array,avg,i,threshold) and array[i+1] != avg:
            LeftEdges.append(i)
        if isRightStraight(array,avg,i,threshold) and array[i-1] != avg:
            RightEdges.append(i)
    return LeftEdges,RightEdges

#находим левые и правые края по следующим условиям
# для левого края - слева определенное кол-во точек равны 0, а следующая точка отличается
# для правого края - справа определенное кол-во точек равны 0, а предыдущая точка отличается
def findEdgesNormal(array, threshold):
    LeftEdges = []
    RightEdges = []
    for i in range(threshold,len(array)-threshold):
        if isLeftStraight(array,0,i,threshold) and array[i+1] != 0:
            LeftEdges.append(i)
        if isRightStraight(array,0,i,threshold) and array[i-1] != 0:
            RightEdges.append(i)
    return LeftEdges,RightEdges


# проверяет, являюются ли n точек слева прямой линией равно avg
def isLeftStraight(array,avg,index,threshold):
    isleftStr = True
    for i in range(threshold):
        isleftStr = isleftStr and array[index-i] == avg
        if not isleftStr:
            return False
    return isleftStr;

# проверяет, являюются ли n точек справа прямой линией равно avg
def isRightStraight(array,avg,index,threshold):
    isrightStr = True
    for i in range(threshold):
        isrightStr = isrightStr and array[index+i] == avg
        if not isrightStr:
            return False
    return isrightStr;

#находим индекс максимального значения в определенном промежутке
def findPeakIndex(array, start, end):
    interval = array[start:end]
    maxValue = max(interval)
    return array.index(maxValue,start-1,end+1)

# оставляем только 1 тон
def first_tone(array, first_tones):
    b= []
    c = []
    def split(arr, size):
         arrs = []
         while len(arr) > size:
             pice = arr[:size]
             arrs.append(pice)
             arr   = arr[size:]
         arrs.append(arr)
         return arrs
    first_tones = split(first_tones, 3)
    for i in range(1, len(first_tones)):
        b.append(first_tones[i][0] - first_tones[i-1][2])
    for i in range(1, len(first_tones)):
        for j in range(1, len(b)):
            if i == j:
                if sum(array[first_tones[i][0]:first_tones[i][2]+1])>sum(array[first_tones[i-1][0]:first_tones[i-1][2]+1]) and b[j]<b[j-1]:
                    c.append(first_tones[i-1])
    print(c)
    return c
# собираем данные в класс Tone, упаковываем
def makeTones(leftEdges,rightEdges,minLenforTone):
    tones = []
    first_tones = []
    if(rightEdges[0] < leftEdges[0]):# если у нас есть правый край, но у него нет парного левого края - удаляем его
        rightEdges.pop(0)
    if leftEdges[-1] > leftEdges[-1]: # если есть левый край, но нет парного правого - удаляем
        leftEdges.pop(len(leftEdges)-1)

    for i in range(min(len(leftEdges),len(rightEdges))): # для каждого левого и правого края создаем объект Tone, и в промежутке этих краев находим значение максимальной амплитуды
        if(rightEdges[i] - leftEdges[i] > minLenforTone):
            peak = findPeakIndex(array,leftEdges[i],rightEdges[i])
            first_tones.append(leftEdges[i])
            first_tones.append(peak)
            first_tones.append(rightEdges[i])
    first_tones = first_tone(array, first_tones)
    for i in range(len(first_tones)):
        tones.append(Tone(first_tones[i][0],first_tones[i][1],first_tones[i][2]))
    return tones


# собираем данные в класс Tone, упаковываем
def makeTonesOld(leftEdges,rightEdges,minLenforTone):
    tones = []
    if(rightEdges[0] < leftEdges[0]):# если у нас есть правый край, но у него нет парного левого края - удаляем его
        rightEdges.pop(0)
    if leftEdges[-1] > leftEdges[-1]: # если есть левый край, но нет парного правого - удаляем
        leftEdges.pop(len(leftEdges)-1)

    for i in range(min(len(leftEdges),len(rightEdges))): # для каждого левого и правого края создаем объект Tone, и в промежутке этих краев находим значение максимальной амплитуды
        if(rightEdges[i] - leftEdges[i] > minLenforTone):
            peak = findPeakIndex(array,leftEdges[i],rightEdges[i])
            tones.append(Tone(leftEdges[i],rightEdges[i],peak))
    return tones

dir_name = 'C:\\Users\\Task\\'
with open(dir_name+'10_PHONO.csv', newline='') as data:
    array = list(map(lambda x: int(x),data.read().split(";")))

#KAA:
#med = median(array)
med = mean(array)


denoisePercent = med/3.5    #уровень шумоподавления
threshold = 20          #сколько точек должны идти прямо, чтобы считаться началом или концом тона
toneMinLen = 5         #какая минимальная длина должна быть у реального тона(нужна чтобы отсечь фейковые всплески)

#KAA: 

array = list(map(lambda x: normalize(x,med),array)) # нормализуем прочитанный массив
windowLength = 50
for idx in range(len(array)-windowLength): #усреднение по скользящему окну размера windowLength - убираем высокие частоты
    array[idx] = sum(array[idx:idx+windowLength-1])/windowLength


#array = list(map(lambda x: denoise(x,med,denoisePercent),array)) # убираем шумы из прочитанного массива

maxVal = max(array)
med = mean(array)
st = sqrt(var(array)) # стандартное отклонение
cutoffValue = (med + st)/3.1

array = list(map(lambda x: cutOff(x,cutoffValue),array)) # убираем фоновые шумы из нормализованного массива
#print(array)

#LeftEdges, RightEdges = findEdges(array, med,threshold) # находим края
LeftEdges, RightEdges = findEdgesNormal(array, threshold) # находим края
#KAA--

tones = makeTones(LeftEdges,RightEdges,toneMinLen) # по данным о краях создаем тоны
#рисуем основной график
pyplot.figure(figsize=(10,3))
pyplot.title(dir_name+'10_PHONO.csv')
pyplot.plot(array,color="lightgray");

#добавляем вертикальные полосы, отображающие тон

pyplot.vlines(x=[l.LeftPoint for l in tones],color="green",ymin=0,ymax=cutoffValue) #начало тона
pyplot.vlines(x=[r.RightPoint for r in tones],color="red",ymin=0,ymax=cutoffValue) # конец тона
pyplot.vlines(x=[p.Peak for p in tones],color="blue",ymin=0,ymax = cutoffValue)# пик тона
pyplot.show()

