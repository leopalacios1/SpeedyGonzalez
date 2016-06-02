## NOTE
# Next update:
# - audio support
# - highscore file support
# - lane number and sizeGame flexibility improvement
# - "how to play" tutorial  

import random
OriginalDesignWidth=800
OriginalDesignHeight=600
sizeGame=1
widthGame=int(OriginalDesignWidth*sizeGame)
heightGame=int(OriginalDesignHeight*sizeGame)
sRatio=float(widthGame)/OriginalDesignWidth
nLanes=7
perspectivePoint=[widthGame*0.5,-15/12*heightGame]
pp2=[]+perspectivePoint
objectUnit=int(widthGame/16)

def getSlope(y,x):
    if x==perspectivePoint[0]:
        slope=0
    else:slope=float((y-perspectivePoint[1])) / float((perspectivePoint[0]-x))
    return slope

class game():
    def __init__(self,nLanes,w,h):
        self.nLanes=nLanes
        self.nPlayers=1
        self.pMode=None
        self.ColorW=None
        self.stdHighway=7           #width based on a 7 lane highway
        self.laneWidth=(14*objectUnit)/self.stdHighway  
        self.asphaltStart=(w-self.laneWidth*self.nLanes)*0.5    #x coordinate of the highway in the bottom left corner
        self.w=w
        self.h=h
        self.frameY=0        # dynamic y coordinate of the 'top' of the screen
        self.vFrame=0
        self.count=0
        self.screenBottom=h
        self.horizonSky=4*objectUnit     # y coordinate where the highway ends
        self.Background=[]  
        self.Trees=[]
        self.Lines=[]
        self.Cars=[]
        self.Taxis=[]
        self.objectID=0
        self.car=[]
        self.skyColor=[random.randint(50,85),random.randint(100,150),random.randint(145,245)]
        self.linesHorizon=[]
        self.laneSlope=[]           # list of slopes of the highway lines, for future use
        self.previousY=60 # variable for highway line generation
        for lane in range(self.nLanes+1):
            self.laneSlope.append(getSlope(self.h,objectUnit+self.laneWidth/2*(1+2*lane)))
        self.setLim()
        self.gameState=1        # indicates the state of the game. Menu1-3, playing, transitions 
        self.shiftY=0
        self.lvl=1
        self.time=0
        self.menu=1
    def setLim(self):
        lineSlope1=getSlope(self.screenBottom,self.asphaltStart)
        lineSlope2=getSlope(self.screenBottom,self.w-self.asphaltStart)             # set grass limits on x plane
        self.xMax1=self.asphaltStart+(self.h-self.horizonSky)/lineSlope1
        self.xMin2=self.asphaltStart+self.nLanes*self.laneWidth+(self.h-self.horizonSky)/lineSlope2
        for lane in range(self.nLanes+1):
            x=self.asphaltStart+lane* self.laneWidth
            lineSlope=getSlope(self.screenBottom,x)
            self.linesHorizon.append(x+(self.h-self.horizonSky)/lineSlope)           # calculation of key coordinates to display background
    def printLines(self):
        strokeWeight(2)
        stroke(0)
        for l in self.Lines:                    # highway lane lines
            if l.relY<=self.h+objectUnit:   
                l.update()
                l.setSizeR()
                l.printObject()
            else: del self.Lines[self.Lines.index(l.ID)]

        line(0,self.horizonSky+self.shiftY,widthGame,self.horizonSky+self.shiftY)       #horizon line
        for lane in [0,len(self.linesHorizon)-1]:
            line(self.asphaltStart+lane*self.laneWidth, self.h+self.shiftY, self.linesHorizon[lane], self.horizonSky+self.shiftY)  #highway borders
        noFill()
        rect(0,0,self.w,self.h) # frame lines
        fill(0)     
    def setBackground(self):
        stroke(0)
        self.count+=1
        fill(self.skyColor[0],self.skyColor[1],self.skyColor[2])        
        rect(0,0,widthGame,self.horizonSky+self.shiftY)         #sky rectangle
        fill(80,80,80)
        stroke(80)
        rect(self.xMax1,self.horizonSky+self.shiftY,self.xMin2-self.xMax1,heightGame+self.shiftY-self.horizonSky)           #highway square
        triangle(self.xMax1,self.horizonSky+self.shiftY,self.asphaltStart,self.h+self.shiftY,self.xMax1,self.h+self.shiftY)             #highway trianlges
        triangle(self.xMin2,self.horizonSky+self.shiftY,self.w-self.asphaltStart,self.h+self.shiftY,self.xMin2,self.h+self.shiftY)
        if self.frameY!= 0 and (-self.frameY+self.previousY)>=70*sRatio:               # game logic - change
            self.previousY=self.frameY
            for lane in range(1,self.nLanes):
                self.Lines.append(highwayLines(game1.linesHorizon[lane],game1.frameY))
                #self.Trees.append(tree())
            if random.random()>0:
                self.Trees.append(tree())
            if random.random()>0.3 and self.gameState!=2:
                ranNum=random.random()
                if ranNum<0.2:          
                    game1.Cars.append(limousine(random.randint(1,self.nLanes),self.frameY+self.horizonSky,self.vFrame+(random.randint(3,7)+game1.lvl)*sRatio))#)int(self.lvl*0.2*random.randint(4,8))))
                elif ranNum<0.5:
                    game1.Cars.append(truck(random.randint(1,self.nLanes),self.frameY+self.horizonSky,self.vFrame+(random.randint(3,7)+game1.lvl)*sRatio))#int(self.lvl*0.2*random.randint(4,8))))
                else:  
                    game1.Cars.append(vehicle(random.randint(1,self.nLanes),self.frameY+self.horizonSky,self.vFrame+(random.randint(3,7)+game1.lvl)*sRatio))#nt(self.lvl*0.2*random.randint(4,8))))
        self.printLines()
    def printObjects(self):
        for obj in self.Background:
            if obj.x<self.w+2*objectUnit or obj.x>0-2*objectUnit:   
                obj.printObject()
            else: del self.Background[self.Background.index(obj.ID)]         # delete those background elements out of frame
        tmp=[]
        for t in self.Trees:
            tmp.append(t)
        for c in self.Cars:
            tmp.append(c)               # organize by increasing y to avoid wrong overlaping in print
        for t in self.Taxis:
            tmp.append(t)
        tmp.sort(key=lambda obj: obj.y)      
        for obj in tmp:
            if obj.relY<(self.h+2*objectUnit) and obj.x>(0-2*objectUnit) and obj.x<(self.w+2*objectUnit):       # delete those background elements out of frame
                obj.printObject()
            elif obj=='tree':
                del self.Trees[self.Trees.index(obj.ID)]  
            elif obj=='vehicle':
                del self.Cars[self.Cars.index(obj.ID)]  
        #obj.printObject()
    def updateObjects(self):
        for t in self.Trees:
            t.update()
        for c in self.Cars:
            c.update()
        for b in self.Background:
            b.update()
        for t in self.Taxis:
            t.update()
    def update(self):
        if  self.gameState==1: 
            self.Taxis=[]
            self.vFrame=-1*sRatio
            self.frameY+=self.vFrame
            perspectivePoint[1]=-15/12*self.h+self.frameY
            self.screenBottom+=self.vFrame 
            self.shiftY=self.h/3
            self.score=0
            self.time=0
            self.setBackground()            
            self.updateObjects()
            self.printObjects()
            textAlign(CENTER)
            fill(0)
            strokeWeight(2)
            stroke(0)
            textSize(72*int(sRatio))
            rect(self.w/2-2*objectUnit,self.h/3+0.5*objectUnit,4*objectUnit,3*objectUnit,7)
            text('Speedy Gonzales',self.w/2,self.h/3)
            fill(255)
            textSize(20*int(sRatio))
            if self.menu==1:
                text('Single Player',self.w/2,self.h/3+1.1*objectUnit)
                text('Multiplayer',self.w/2,self.h/3+1.8*objectUnit)
                text('High Scores',self.w/2,self.h/3+2.5*objectUnit)
                text('Credits',self.w/2,self.h/3+3.2*objectUnit)
            elif self.menu==2:
                text('Start',self.w/2,self.h/3+1.5*objectUnit)
                text('Main Menu',self.w/2,self.h/3+2.75*objectUnit)
            elif self.menu==3:
                text('Vs.',self.w/2,self.h/3+1.35*objectUnit)
                text('Co-op',self.w/2,self.h/3+2.15*objectUnit)
                text('Main Menu',self.w/2,self.h/3+2.95*objectUnit)
            elif self.menu==4:
                text('Play Again',self.w/2,self.h/3+1.5*objectUnit)
                text('Main Menu',self.w/2,self.h/3+2.75*objectUnit)
            elif self.menu==5:
                text('HIGHSCORES',self.w/2,self.h/3+1.1*objectUnit)
                textSize(15*int(sRatio))
                text('Single Player',self.w/2,self.h/3+1.8*objectUnit)
                text('Co-op',self.w/2,self.h/3+2.5*objectUnit)
                text('Main Menu',self.w/2,self.h/3+3.2*objectUnit)
            elif self.menu==6:
                textSize(15*int(sRatio))
                text('Leonardo',self.w/2,self.h/3+1.35*objectUnit)
                text('Palacios',self.w/2,self.h/3+2.15*objectUnit)
                text('Main Menu',self.w/2,self.h/3+2.95*objectUnit)


        elif self.gameState==2:
            self.time+=1
            self.frameY+=self.vFrame
            perspectivePoint[1]=-15/12*self.h+self.frameY
            self.screenBottom+=self.vFrame 
            mes='Ready'
            if self.shiftY>=0:
                self.shiftY+=-1*sRatio
            if self.time>180:
                self.gameState=3   
                self.previousFramey=self.frameY
                self.time=0 
            elif self.time>160:
                mes='Go!'
            elif self.time>90:
                mes='Set'
            if not game1.Taxis:
                if self.nPlayers==1:
                    game1.Taxis.append(taxi(int(game1.nLanes*0.5+0.5),heightGame-objectUnit,self.vFrame))
                elif self.nPlayers==2:
                    game1.Taxis.append(taxi(int(game1.nLanes*0.5+1.5),heightGame-objectUnit,self.vFrame))
                    game1.Taxis.append(taxi(int(game1.nLanes*0.5-0.5),heightGame-objectUnit,self.vFrame))
            self.setBackground()       
            self.updateObjects()
            self.printObjects()
            game1.Taxis[0].printObject()
            fill(0)
            if self.time!=0:
                rect(self.w/2-2*objectUnit,self.h/3+1*objectUnit,4*objectUnit,2*objectUnit,7)
                textSize(45*int(sRatio))
                fill(225)
                text(mes,self.w/2,self.h/3+2.5*objectUnit)
            if abs(game1.Taxis[0].y-(heightGame-objectUnit))<1*sRatio:
                game1.Taxis[0].vy=self.vFrame
        elif self.gameState==3:  
            self.time+=1
            if self.time%600==0:
                print('LEVEL UP') # Make sound
                self.vFrame+=-2*self.lvl
                for t in self.Taxis:
                    t.vy+=-2*self.lvl
            if -self.frameY+self.previousFramey>80:
                self.previousFramey=self.frameY
                self.score+=50
            self.frameY+=self.vFrame
            perspectivePoint[1]=-15/12*self.h+self.frameY
            self.screenBottom+=self.vFrame 
            self.setBackground()       
            self.updateObjects()
            self.printObjects()
            self.checkCollision()
            if self.pMode!='VS':
                fill(0)
                rect(self.w/2-1.25*objectUnit,self.h/3-3*objectUnit,2.5*objectUnit,2.25*objectUnit,7)
                fill(255)
                textSize(30*int(sRatio))
                text('Score',self.w/2,self.h/3-2*objectUnit) 
                textSize(30*int(sRatio))
                fill(255)
                textSize(35*int(sRatio))
                text(self.score,self.w/2+2,self.h/3-1*objectUnit) 
            keyControl()
        elif self.gameState==4:     #crash
            self.time+=1
            if self.time%200==0:
                self.gameState=1
                self.menu=4

            strokeWeight(2)
            stroke(0)
            if self.vFrame<-1:
                self.vFrame+=1
                for c in self.Cars:
                    c.vy+=1
                for t in self.Taxis:
                    t.vy+=2
            self.frameY+=self.vFrame
            perspectivePoint[1]=-15/12*self.h+self.frameY
            if self.shiftY<=self.h/3:
                self.shiftY+=2
            self.screenBottom+=self.vFrame 
            self.setBackground()            
            self.updateObjects()
            self.printObjects()
            if self.pMode!='VS':
                fill(0)
                rect(self.w/2-1.25*objectUnit,self.h/3-3*objectUnit+self.shiftY,2.5*objectUnit,2.25*objectUnit,7)
                fill(255)
                textSize(30*int(sRatio))
                text('Score',self.w/2,self.h/3-2*objectUnit+self.shiftY) 
                fill(255)
                textSize(35*int(sRatio))
                text(self.score,self.w/2+2,self.h/3-1*objectUnit+self.shiftY) 
            else:
                 fill(0)
                 rect(self.w/2-3*objectUnit,self.h/3-3*objectUnit+self.shiftY,6*objectUnit,1.25*objectUnit,7)
                 fill(self.colorW[0][0],self.colorW[0][1],self.colorW[0][2])
                 textSize(40*int(sRatio))
                 text(self.colorW[1]+' wins!',self.w/2,self.h/3-2*objectUnit+self.shiftY) 
            fill(0)
            textSize(72*int(sRatio))
            text('Speedy Gonzales',self.w/2,self.h/3-(self.h/3-self.shiftY))
    def checkCollision(self):
        for t in self.Taxis:
            x1=t.xMap
            x2=x1+t.wMap
            y1=t.yMap
            y2=t.y2Map
            for c in self.Cars:
                x3=c.xMap
                x4=x3+c.wMap
                y3=c.yMap
                y4=c.y2Map
                for p in [[x3,y3],[x3,y4],[x4,y3],[x4,y4]]:
                    if p[0]>=x1 and p[0]<=x2 and p[1]<=y1 and p[1]>=y2:
                        self.gameState=4
                        t.vy=c.vy
                        self.time=0 #make sound
                        if self.Taxis.index(t.ID)==0:
                            self.colorW=[[0,0,255],'Blue']
                        else: self.colorW=[[255,0,0],'Red']
    
game1=game(nLanes,widthGame,heightGame) 

class StationaryObject():
    def __init__(self,x0,y0):
        self.x=x0
        self.ID=game1.objectID
        game1.objectID+=1
        self.y=y0
        self.relY=y0-game1.frameY
        self.TraySlope=getSlope(y0,x0)
        self.setSizeR()
    def __eq__(self,other):
        return self.ID == other
    def update(self):        
        self.relY=self.y-game1.frameY
        self.x=self.setX(self.y)
        self.setSizeR()
    def setX(self,y,slope=None):
        if slope is None:
            slope=self.TraySlope
        if slope!=0:
            return (slope*perspectivePoint[0]+perspectivePoint[1]-y)/(slope)
        else:
            return perspectivePoint[0]
    def setSizeR(self):
        self.sizeR=.7+(float((self.relY-game1.horizonSky))/float((game1.h-game1.horizonSky)))*.3#.7+((self.y-(game1.frameY+game1.horizonSky))*(game1.screenBottom-(game1.frameY+game1.horizonSky))**-1)*.3

class highwayLines(StationaryObject):
    def __init__(self,x,y=game1.frameY):
        self.x=x
        self.ID=game1.objectID
        game1.objectID+=1
        self.y=y
        self.relY=self.y
        self.TraySlope=getSlope(game1.horizonSky+game1.frameY,x)
        self.l=objectUnit/2
        self.setSizeR()
        theta=atan(self.TraySlope)    
    def printObject(self):
        stroke(255)
        strokeWeight(2)
        self.x=self.setX(self.y)
        theta=atan(self.TraySlope)
        if self.relY<game1.horizonSky:
            tmp=game1.horizonSky+game1.shiftY
        else: tmp=self.relY+game1.shiftY
        if self.TraySlope>=0 and self.relY+self.l*self.sizeR*sin(theta)>= game1.horizonSky:
            line(self.x, tmp, self.x-self.l*self.sizeR*cos(theta), self.relY+self.l*self.sizeR*sin(theta)+game1.shiftY)
        elif self.relY-self.l*self.sizeR*sin(theta)>= game1.horizonSky:
            line(self.x, tmp, self.x+self.l*self.sizeR*cos(theta), self.relY-self.l*self.sizeR*sin(theta)+game1.shiftY) 
        stroke(0)
    def update(self):
        self.x=self.setX(self.y)        
        self.relY=self.y-game1.frameY
        self.setSizeR()
      
class tree(StationaryObject):
    def __init__(self):
        y0=game1.horizonSky+game1.frameY
        self.nLeafs=190
        self.TrunkW=(0.25+random.random()*0.1)*objectUnit
        self.TrunkH=(0.9+random.random()*0.2)*objectUnit
        if random.randint(0,1)==0:
            x=random.random()*game1.xMax1
        else:x=game1.xMin2+random.random()*(game1.w-game1.xMin2)
        StationaryObject.__init__(self,x,y0)
        self.leafs=[]
        diameterGreen=(0.9+random.random()*0.2)*objectUnit
        ratioL=4
        self.leafDiam=diameterGreen/ratioL
        blueVal=0
        for leaf in range(1,self.nLeafs+1):
            angle=random.random()*2*PI
            r=random.random()*0.6*objectUnit
            PyCo=-(self.TrunkH+sin(angle)*r)
            PxCo=cos(angle)*r
            redVal=random.randint(0,102)
            greenVal=102+random.randint(0,102)        
            self.leafs.append([PxCo,PyCo,redVal,greenVal,blueVal])       
    def __eq__(self,other):
        return (self.ID == other or 'tree'==other)
    def printObject(self):
        strokeWeight(2)
        stroke(0) 
        fill(92,92,13)
        strokeWeight(1)
        if self.x>=game1.xMin2:
            rect(self.x,self.relY-self.TrunkH*self.sizeR+game1.shiftY,self.sizeR*self.TrunkW,self.sizeR*self.TrunkH)
            for leaf in self.leafs:
                fill(leaf[2],leaf[3],leaf[4])
                ellipse(self.x-(self.sizeR*leaf[0])+self.sizeR*self.TrunkW*0.5,self.relY+self.sizeR*leaf[1]+game1.shiftY,self.sizeR*self.leafDiam,self.sizeR*self.leafDiam)

        elif self.x<=game1.xMax1:
            rect(self.x-self.TrunkW*self.sizeR,self.relY-self.TrunkH*self.sizeR+game1.shiftY,self.sizeR*self.TrunkW,self.sizeR*self.TrunkH)
            for leaf in self.leafs:
                fill(leaf[2],leaf[3],leaf[4])
                ellipse(self.x-0.5*self.TrunkW*self.sizeR+self.sizeR*leaf[0],self.relY+self.sizeR*leaf[1]+game1.shiftY,self.sizeR*self.leafDiam,self.sizeR*self.leafDiam)

class MovingObject(StationaryObject):
    def __init__(self,x0,y0,v0):
        StationaryObject.__init__(self,x0,y0)
        self.vy=v0
        self.vx=0
    def update(self):
        self.x+=self.vx
        self.y+=self.vy
        self.relY=self.y-game1.frameY
        self.setSizeR

class sun(MovingObject):
    def __init__(self):
        if random.randint(0,1)==1:
            v=objectUnit*(700*(1+random.random()))**-1
            x=4*objectUnit*random.random()
        else:
            v=-objectUnit*(700*(1+random.random()))**-1
            x=game1.w-int(4*objectUnit*random.random())
        y=int(objectUnit*2*random.random())
        self.r=objectUnit*(1+random.random())
        MovingObject.__init__(self,x,y,0)
        self.vx=v
        self.colors=[255,222+13*random.random(),100*random.random()]
    def update(self):
        if game1.vFrame!=0:
            self.x+=self.vx
    def printObject(self):
        fill(self.colors[0],self.colors[1],self.colors[2])
        ellipse(int(self.x),self.y+game1.shiftY,self.r,self.r)  

class clouds(MovingObject,):
    def __init__(self,x):
        if random.randint(0,1)==1:
            v=objectUnit*(500*(1+random.random()))**-1
        else:
            v=-objectUnit*(500*(1+random.random()))**-1
        if random.randint(0,1)==1:
            y=int(objectUnit*(-3.2+1.05*random.random()))  
        else:
            y=int(objectUnit*(1+random.random()))  
                                                                                                                                                               
        self.r=objectUnit*(0.2+(random.random())*.3)
        MovingObject.__init__(self,int(x),y,0)
        self.vx=v
        nOvals=3
        self.ovalInfo=[]
        startAngle=(2*PI*random.random())
        for rep in range(nOvals):
            color=190+65*random.random()
            x=self.x+int(cos(startAngle+rep*(2*PI)/nOvals)*self.r)
            y=self.y+int(sin(startAngle+rep*(2*PI)/nOvals)*self.r)
            w=(0.5+0.5*random.random())*objectUnit
            h=(1+0.5*random.random())*objectUnit
            self.ovalInfo.append([color,x,y,h,w])
    def update(self):
        if game1.vFrame!=0:
            for o in self.ovalInfo:
                o[1]+=self.vx
    def printObject(self):
        for o in self.ovalInfo:
            fill(o[0],o[0],o[0])
            ellipse(o[1],o[2]+game1.shiftY,o[3],o[4])  

class mountains(MovingObject):
    def __init__(self):
        y=int(objectUnit*(2+random.random()))
        x=int(game1.w/9*(1+random.random()))
        MovingObject.__init__(self,x,y,0)
        if random.randint(0,1)==1:
            self.vx=objectUnit*(1800*(1+random.random()))**-1
        else:
            self.vx=-objectUnit*(1800*(1+random.random()))**-1
        nMountains=3
        self.colors=[]
        for m in range(nMountains):
            self.colors.append(random.randint(100,160))
        self.x2=int(objectUnit*(7+2*random.random()))
        self.x3=int(objectUnit*(13+2*random.random()))
        self.y2=self.y-objectUnit*random.random()
        self.y3=self.y+objectUnit*(-1+2*random.random())
        self.baseA1=int(objectUnit*random.random())
        self.baseA2=int(objectUnit*(5+random.random()))
        self.baseB1=int(objectUnit*(3+random.random()))
        self.baseB2=int(objectUnit*(11+random.random()))
        self.baseC1=int(objectUnit*(9+2*random.random()))
        self.baseC2=int(game1.w+objectUnit*random.random())
    def update(self):
        if game1.vFrame!=0 and game1.gameState==3: #If player is playing and perspective is moving:
            self.x+=self.vx
            self.x2+=self.vx
            self.x3+=self.vx
            self.baseA1+=self.vx
            self.baseA2+=self.vx
            self.baseB1+=self.vx
            self.baseB2+=self.vx
            self.baseC1+=self.vx
            self.baseC2+=self.vx
    def printObject(self):
        fill(0,self.colors[0],0)
        triangle(self.x,self.y+game1.shiftY,  self.baseA1,game1.horizonSky+game1.shiftY,  self.baseA2,game1.horizonSky+game1.shiftY)
        fill(0,self.colors[1],0)
        triangle(self.x2,self.y2+game1.shiftY,self.baseB1,game1.horizonSky+game1.shiftY,self.baseB2,game1.horizonSky+game1.shiftY)
        fill(0,self.colors[2],0)
        triangle(self.x3,self.y3+game1.shiftY,self.baseC1,game1.horizonSky+game1.shiftY,self.baseC2,game1.horizonSky+game1.shiftY)

class cuboid():
  def __init__(self,x,y,w,h,d,color):
        self.color=color
        self.x=x
        self.y=y
        self.w=w
        self.h=h
        self.d=d
        self.relY=y-game1.frameY
        self.getPoints()
  def getPoints(self):
        self.point1=[self.x,self.relY]   
        self.point2=[self.x+self.w,self.relY]
        self.point3=[self.x,self.relY+self.h]
        self.point4=[self.x+self.w,self.relY+self.h]
        self.xAndys=[]   
        for p in [self.point1,self.point2,self.point3,self.point4]:
              slope=getSlope(p[1],p[0])
              theta=atan(slope)
              if slope>0:
                  self.xAndys.append([p[0]+self.d*cos(theta),p[1]-self.d*sin(theta)])
              elif slope==0:
                  self.xAndys.append([p[0],p[1]+self.d*sin(theta)]) 
              else:
                  self.xAndys.append([p[0]-self.d*cos(theta), p[1]+self.d*sin(theta)]) 
        self.point5=self.xAndys[0]
        self.point6=self.xAndys[1]
        self.point7=self.xAndys[2]
        self.point8=self.xAndys[3]
  def printObject(self):
        stroke(0)
        strokeWeight(1)
        fill(self.color[0],self.color[1],self.color[2])
        quad(self.point2[0],self.point2[1]+game1.shiftY,self.point6[0],self.point6[1]+game1.shiftY,self.point8[0],self.point8[1]+game1.shiftY,self.point4[0],self.point4[1]+game1.shiftY)
        quad(self.point1[0],self.point1[1]+game1.shiftY,self.point5[0],self.point5[1]+game1.shiftY,self.point7[0],self.point7[1]+game1.shiftY,self.point3[0],self.point3[1]+game1.shiftY)
        quad(self.point1[0],self.point1[1]+game1.shiftY,self.point2[0],self.point2[1]+game1.shiftY,self.point6[0],self.point6[1]+game1.shiftY,self.point5[0],self.point5[1]+game1.shiftY)
        quad(self.point1[0],self.point1[1]+game1.shiftY,self.point2[0],self.point2[1]+game1.shiftY,self.point4[0],self.point4[1]+game1.shiftY,self.point3[0],self.point3[1]+game1.shiftY)
        strokeWeight(1)

class pairCub(cuboid):
  def __init__(self,x,y,distBet,w,h,d,color1,color2=None):
        self.distBet=distBet
        if color2==None:
            self.color2=color1
        else: self.color2=color2
        cuboid.__init__(self,x,y,w,h,d,color1)
  def printObject(self):
        cuboid.printObject(self)
        self.pair.printObject()
  def getPoints(self):
        cuboid.getPoints(self)
        self.pair=cuboid(self.x+self.distBet,self.y,self.w,self.h,self.d,self.color2)
        
class vehicle(MovingObject):
  def __init__(self,lane,y,v,color=None):
        self.Taxi=False
        self.lane=lane
        self.cw=0.8*objectUnit
        self.switching=[False,0]
        self.sizeR=.7+(float((y-game1.frameY-game1.horizonSky))/float((game1.h-game1.horizonSky)))*.3
        MovingObject.__init__(self,self.XinLane(y),y,v)
        if color is None:
            self.color=[random.randint(0,255),random.randint(0,255),random.randint(0,255)]
        else: self.color=color
        self.ch=0.5*objectUnit
        self.cd=0.6*objectUnit
        # Light Control Attributes
        self.Lmode={'TurnL':False,'TurnR':False}
        self.onColor=[255,255,255]#[255,255,47]
        self.offColor=[140,140,140]
        flashTime=1
        gameFrameRate=60
        self.changeAt=flashTime*gameFrameRate
        self.count=0
        self.lightControl()   
        self.color0=self.color      #change chasis color for players
        self.tmpVelocity=self.vy
        self.update()
        self.coordinatesMap()
  def __eq__(self,other):
        return (self.ID == other or 'vehicle'==other)
  def update(self):
        self.setSizeR()
        self.lightControl()
        MovingObject.update(self)
        if not self.switching[0]:
            self.x=self.setX(self.y,game1.laneSlope[self.lane-1])-self.cw*self.sizeR*0.5
        w=self.cw*self.sizeR
        h=self.ch*self.sizeR
        d=self.cd*self.sizeR
        self.Parts=[]
        self.window=cuboid(self.x+w/4,  self.y- 0.45*h, w*1/2, 0.3*h, 0, [50,255,255])
        self.chasis2=cuboid(self.x+w/8, self.y-0.6*h,   w*3/4, 0.6*h, d, self.color)
        self.chasis0=cuboid(self.x,     self.y,         w,     h*1/7, d, self.color0)
        self.chasis1=cuboid(self.x,     self.y+h*1/7,   w,     2*h/7, d, self.color)
        self.Lights=pairCub(self.x,  self.y+h*1/7, 3*w/4, w/4, h/7,   d/8, self.lightColor1, self.lightColor2)
        self.cauchos=pairCub(self.x, self.y+h*3/7, 3*w/4, w/4, h*2/7, d/8, [0,0,0])
        self.Parts=[self.cauchos,self.chasis1,self.Lights,self.chasis0,self.chasis2,self.window]
        self.laneChange('U')
        self.coordinatesMap()
        if not self.Taxi:
            self.AI()
  def printObject(self):
        for p in self.Parts:
            p.printObject()
  def XinLane(self,y=None,lane=None):
        if lane is None:
            lane=self.lane
        slope=game1.laneSlope[lane-1]
        if slope!=0:
            return (slope*pp2[0]+pp2[1]-y)/(slope)-self.cw*self.sizeR*0.5
        else:
            return pp2[0]-self.cw*self.sizeR*0.5     
  def laneChange(self,direction='U',v=2):
        if direction=='L' and self.lane!=1 and self.switching[0]==False:
            self.changeLightMode('L')
            self.vx=-v*sRatio
            self.switching=[True,self.XinLane(self.relY,self.lane-1)]
            self.lane=self.lane-1
        elif direction=='R' and self.lane!=game1.nLanes and self.switching[0]==False:
            self.changeLightMode('R')
            self.vx=v*sRatio
            self.switching=[True,self.XinLane(self.relY,self.lane+1)]
            self.lane=self.lane+1
        elif direction=='U':
            if abs(self.x-self.switching[1])<=5:
                self.vx=0
                self.changeLightMode('DONE')
                self.switching=[False,0]
                self.vy=self.tmpVelocity
  def lightControl(self):
        self.changeLightMode('OFF')
        if self.Lmode['TurnL']:
            if self.count%self.changeAt<=self.changeAt/2:
                self.changeLightMode('L')
                self.count+=1
        elif self.Lmode['TurnR']:
            if self.count%self.changeAt<=self.changeAt/2:
                self.changeLightMode('R')
                self.count+=1
        else:
            self.count=0
  def changeLightMode(self,m):
      if m=='L':
          self.lightColor1=self.onColor
          self.lightColor2=self.offColor
          self.Lmode['TurnL']=True
      elif m=='R':
          self.lightColor1= self.offColor
          self.lightColor2=self.onColor
          self.Lmode['TurnR']=True
      elif m=='OFF':
          self.lightColor1= self.offColor
          self.lightColor2= self.offColor
      elif m=='DONE':
          self.Lmode['TurnR']=False
          self.Lmode['TurnL']=False
  def coordinatesMap(self):
        self.yMap=self.chasis0.point1[1] 
        self.xMap,self.wMap=self.setRelX(self.chasis0.point1[0],self.chasis0.point1[1])
        self.y2Map=self.chasis0.point5[1] 
  def setRelX(self,x,y):
        slope1=game1.laneSlope[0]
        slope2=game1.laneSlope[game1.nLanes]
        x1=(slope1*pp2[0]+pp2[1]-y)/(slope1)
        x2=(slope2*pp2[0]+pp2[1]-y)/(slope2)
        xRel=(x-x1)/(x2-x1)
        return xRel,(self.cw/(x2-x1))
  def AI(self):
        for c in game1.Cars:
            if self.lane==c.lane and abs((c.y2Map-self.yMap))<10 and not self.switching[0]:
                self.vy=c.vy
                dir=['L','R']
                self.laneChange(dir[random.randint(0,1)])

class taxi(vehicle):
    def __init__(self,lane,y,v):
        vehicle.__init__(self,lane,y,v)
        self.Taxi=True
        if game1.Taxis:
            self.color=[0,0,255]
            self.color0=[255,255,255]
        else:
            self.color=[255,0,0]
            self.color0=[255,255,255]

        self.bypassD=False
        self.bypassU=False
    def __eq__(self,other):
        return (self.ID == other or 'taxi'==other)
    def update(self):
        vehicle.update(self)
        self.m=self.chasis0.point1[1]-self.chasis0.point5[1]
        if self.relY>=(heightGame-objectUnit) and game1.gameState==3:#!=2 and not self.bypassU:
            self.y=game1.frameY+(heightGame-objectUnit)
        elif self.relY-self.m<=game1.horizonSky:# and not self.bypassD:
            self.y=game1.frameY+game1.horizonSky+self.m
    def laneChange(self,direction='U'):
        vehicle.laneChange(self,direction,8*sRatio)
    def speed(self,uod):
      if uod =='U' and not self.switching[0]:
          self.vy=game1.vFrame-6*sRatio
      elif uod =='D' and not self.switching[0]:
          self.vy=game1.vFrame+6*sRatio
      elif uod == 'R' and not self.switching[0]:
          self.vy=game1.vFrame

class truck(vehicle):
    def __init__(self,lane,y,v):
        vehicle.__init__(self,lane,y,v)
        self.ch=0.7*objectUnit
    def update(self):
        vehicle.update(self)
        w=self.cw*self.sizeR
        h=self.ch*self.sizeR
        d=self.cd*self.sizeR
        self.chasis2=cuboid(self.x, self.y-0.8*h, w, 0.8*h, d, self.color)
        self.chasis0.color=[200,200,200]
        self.chasis1.color=[200,200,200]
        self.cauchos.h=h*3/7
        self.cauchos.getPoints()
        self.Parts=[self.cauchos,self.chasis1,self.Lights,self.chasis0,self.chasis2]

class limousine(vehicle):
    def __init__(self,lane,y,v):
        self.color=[50,50,50]
        vehicle.__init__(self,lane,y,v,self.color)
        self.cd=1.2*objectUnit
        self.ch=0.4*objectUnit
    def update(self):
        vehicle.update(self)
        w=self.cw*self.sizeR
        h=self.ch*self.sizeR
        d=self.cd*self.sizeR
        self.window=cuboid(self.x+w/8,  self.y- 0.45*h, w*3/4, 0.3*h, 0, [50,255,255])
        self.chasis2=cuboid(self.x, self.y-0.6*h,   w, 0.6*h, d, self.color)
        self.Parts=[self.cauchos,self.chasis1,self.Lights,self.chasis0,self.chasis2,self.window]

def setup():
        background(0,50,0)
        size(widthGame,heightGame)
        game1.Background.append(sun())
        for cloud in range(6):
            game1.Background.append(clouds(random.randint(1,11)*objectUnit))
        game1.Background.append(mountains())
        segaFont=loadFont('SEGA.vlw')
        textFont(segaFont)
        game1.vFrame=-3*sRatio
        for rep in range(int(game1.h/60)):
            if random.random()>0.5:
                game1.Trees.append(tree())
            for lane in range(1,game1.nLanes):
                game1.Lines.append(highwayLines(game1.linesHorizon[lane],game1.frameY))
            for l in game1.Lines:
                l.y+=60*sRatio
            for t in game1.Trees:
                t.y+=60*sRatio
def draw():
    background(128,128,0) 
    game1.update()

keyL0={'38': False,'39':False ,'40':False ,'37': False, 'a':False,'s':False,'w':False,'d':False,'s':False}
keyL1={'38': False,'39':False ,'40':False ,'37': False, 'a':False,'s':False,'w':False,'d':False,'s':False}
def keyReleased():
        if keyCode==LEFT or keyCode==RIGHT or keyCode==DOWN or keyCode==UP:
            keyL0[str((keyCode))]=False
        elif key=='a' or key=='A' or key=='d' or key=='D' or key=='W' or key=='w' or key=='s' or key=='S':
           keyL1[(key)]=False

def keyPressed():
        if keyCode==LEFT or keyCode==RIGHT or keyCode==DOWN or keyCode==UP:
            keyL0[str((keyCode))]=True
        elif key=='a' or key=='A' or key=='d' or key=='D' or key=='W' or key=='w' or key=='s' or key=='S':
           keyL1[(key)]=True

def keyControl():   
    if game1.gameState==3:
        
            if keyL0['37']:
                game1.Taxis[0].laneChange('L')
            elif keyL0['39']:
                game1.Taxis[0].laneChange('R')
                
            if keyL0['38']:
                game1.Taxis[0].speed('U')
                game1.Taxis[0].bypassU=True
            elif keyL0['40']:
                game1.Taxis[0].speed('D')
                game1.Taxis[0].bypassD=True
            else:
                game1.Taxis[0].speed('R')
                game1.Taxis[0].bypassD=False
                
            if game1.nPlayers==2:    
                if keyL1['a']:
                    game1.Taxis[1].laneChange('L')
                elif keyL1['d']:
                    game1.Taxis[1].laneChange('R')
                    
                if keyL1['w']:
                    game1.Taxis[1].speed('U')
                    game1.Taxis[1].bypassU=True
                elif keyL1['s']:
                    game1.Taxis[1].speed('D')
                    game1.Taxis[1].bypassD=True
                else:
                    game1.Taxis[1].speed('R')
                    game1.Taxis[1].bypassD=False
    
    
   
def mouseClicked():
    if game1.gameState==1 and mouseX>=game1.w/2-2*objectUnit and mouseX<=game1.w/2+2*objectUnit:
        if game1.menu==1:
            if mouseY>=game1.h/3+0.5*objectUnit and mouseY<=game1.h/3+0.5*objectUnit+3*objectUnit/4:
                game1.menu=2
            elif mouseY>=game1.h/3+0.5*objectUnit+3*objectUnit/4 and mouseY<=game1.h/3+0.5*objectUnit+6*objectUnit/4:
                game1.menu=3
            elif mouseY>=game1.h/3+0.5*objectUnit+6*objectUnit/4 and mouseY<=game1.h/3+0.5*objectUnit+9*objectUnit/4:
                game1.menu=5
            elif mouseY>=game1.h/3+0.5*objectUnit+9*objectUnit/4 and mouseY<=game1.h/3+0.5*objectUnit+3*objectUnit:
                game1.menu=6
        elif game1.menu==2:
            if mouseY>=game1.h/3+0.5*objectUnit and mouseY<=game1.h/3+0.5*objectUnit+6*objectUnit/4:
                game1.menu=1 # and start game
                game1.gameState=2
                game1.nPlayers=1
                game1.pMode='SINGLE'
            elif mouseY>=game1.h/3+0.5*objectUnit+3*objectUnit/4 and mouseY<=game1.h/3+0.5*objectUnit+3*objectUnit:
                game1.menu=1
        elif game1.menu==3:
            if mouseY>=game1.h/3+0.5*objectUnit and mouseY<=game1.h/3+0.5*objectUnit+objectUnit:
                game1.menu=1 # and start game
                game1.gameState=2
                game1.nPlayers=2
                game1.pMode='VS'
            elif mouseY>=game1.h/3+0.5*objectUnit+objectUnit and mouseY<=game1.h/3+0.5*objectUnit+2*objectUnit:
                game1.menu=1# and start game
                game1.gameState=2
                game1.nPlayers=2
                game1.pMode='COOP'
            elif mouseY>=game1.h/3+0.5*objectUnit+2*objectUnit and mouseY<=game1.h/3+0.5*objectUnit+3*objectUnit:
                game1.menu=1
        elif game1.menu==4:
            game1.Taxis=[]
            if mouseY>=game1.h/3+0.5*objectUnit and mouseY<=game1.h/3+0.5*objectUnit+6*objectUnit/4:
                game1.menu=1 # and start game
                game1.gameState=2
            elif mouseY>=game1.h/3+0.5*objectUnit+3*objectUnit/4 and mouseY<=game1.h/3+0.5*objectUnit+3*objectUnit:
                game1.menu=1
        elif game1.menu==5:
            if mouseY>=game1.h/3+0.5*objectUnit and mouseY<=game1.h/3+0.5*objectUnit+objectUnit:
                game1.menu=1 
            elif mouseY>=game1.h/3+0.5*objectUnit+objectUnit and mouseY<=game1.h/3+0.5*objectUnit+2*objectUnit:
                game1.menu=1
            elif mouseY>=game1.h/3+0.5*objectUnit+2*objectUnit and mouseY<=game1.h/3+0.5*objectUnit+3*objectUnit:
                game1.menu=1
        elif game1.menu==6:
            if mouseY>=game1.h/3+0.5*objectUnit and mouseY<=game1.h/3+0.5*objectUnit+objectUnit:
                game1.menu=1 
            elif mouseY>=game1.h/3+0.5*objectUnit+objectUnit and mouseY<=game1.h/3+0.5*objectUnit+2*objectUnit:
                game1.menu=1
            elif mouseY>=game1.h/3+0.5*objectUnit+2*objectUnit and mouseY<=game1.h/3+0.5*objectUnit+3*objectUnit:
                game1.menu=1












