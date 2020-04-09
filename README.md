# Equation Solver with Particle Swarm Optimization

## 1. Import module
```python
import random
import re
import operator as operan
```

## 2. Define The Equation
```python
equation = 'a+2b-3c+4d=30'
```

## 3. Define Operans
```python
ops = { "+": operan.add, 
        "-": operan.sub }
```

## 4. Parse Equation (string) to Real Equation
```python
def break_equation(equation):
  equation_resul = int(equation.split('=')[1])

  equation_body = equation.split('=')[0]
  equation_split_op = re.split(r'[-+]', equation_body)
  number_of_variable = len(equation_split_op)

  operator = []
  for x in list(equation_body):
    if (x == '-' or x == '+'):
      operator.append(x)

  cons = []
  for y in equation_split_op:
    if len(list(y)) == 1:
      cons.append(1)
    else:
      cons.append(int(list(y)[0]))
  
  return cons, operator, equation_resul, number_of_variable
```

## 5. Print Real Equation
```python
cons, operator, equation_resul, number_of_variable = break_equation(equation)
print(cons)
print(operator)
print(equation_resul)
print(number_of_variable)
```

    [1, 2, 3, 4]
    ['+', '-', '+']
    30
    4


## 6. Compute Fitness
```python
def cal_fitness(var_values):
    result = ops[operator[0]]((cons[0]*var_values[0]),(cons[1]*var_values[1]))
    for i in range(1, len(operator)):
        result = ops[operator[i]](result,(cons[i+1]*var_values[i+1]))

    minimasi = abs(equation_resul-result)
    
    return minimasi;
```

## 7. Particle Class
```python
class Particle:
    def __init__(self, initial_position):
        self.position = initial_position
        self.dimensions = len(initial_position)
        self.position_of_best = initial_position 
        self.velocity = [random.uniform(-1,1) for i in range(self.dimensions)]           
        self.error_best = float('inf')        
        self.error = float('inf')
        
    def set_pbest(self):
        self.error = cal_fitness(self.position)
        if self.error < self.error_best or self.error == 0:
            self.position_of_best = self.position
            self.error_best = self.error_best
    
    def compute_velocity(self, w, c1, c2, pg):
        for i in range(self.dimensions):
            r1 = random.random()
            r2 = random.random()
            
            vel_cognitive = c1 * r1 * (self.position_of_best[i]-self.position[i])
            vel_social = c2 * r2 * (pg[i]-self.position[i])
            self.velocity[i] = w * self.velocity[i] + vel_cognitive + vel_social
        
    
    def compute_position(self, limit):
        for i in range(self.dimensions):
            self.position[i] = self.position[i] + self.velocity[i]

            if self.position[i] > limit[i][1]:
                self.position[i] = limit[i][1]

            if self.position[i] < limit[i][0]:
                self.position[i] = limit[i][0]
            
```

## 8. PSO Class
```python
class PSO:
    def __init__(self, initial_position, num_particles, limit, num_iterations, w, c1, c2):
        self.initial_position = initial_position
        self.num_particles = num_particles
        self.limit = limit
        self.num_iterations = num_iterations
        self.w = w
        self.c1 = c1
        self.c2 =c2
        self.error_best_of_group = float('inf')
        self.position_best_of_group = float('inf')
        self.swarm=[]
        
        for i in range(0,num_particles):
            self.swarm.append(Particle(self.initial_position))
            
    def run(self):
        for i in range(self.num_iterations):
            for j in range(self.num_particles):
                self.swarm[j].set_pbest()
                
                if self.swarm[j].error < self.error_best_of_group or self.error_best_of_group == 0:
                    self.position_best_of_group = list(self.swarm[j].position)
                    self.error_best_of_group = float(self.swarm[j].error)
                
            
            for j in range(self.num_particles):
                self.swarm[j].compute_velocity(self.w, self.c1, self.c2, self.position_best_of_group)
                self.swarm[j].compute_position(self.limit)
        
        print('Result:')
        print('best position of the group: ', end='\t')
        print(self.position_best_of_group)
        print('best error of the group: ', end='\t')
        print(self.error_best_of_group)
```

## 9. Example
```python
initial_position = [5  for i in range(number_of_variable)]           
limit =[(0,10),(0,10),(0,10),(0,10)] 
pso = PSO(initial_position=initial_position, num_particles=30, limit = limit, num_iterations=100, w=0.5, c1=1, c2=2)
pso.run()
```

    Result:
    best position of the group: 	[7.560453440543434, 4.099113449433389, 4.104981759840012, 6.636652246547547]
    best error of the group: 	0.009655953919637028
