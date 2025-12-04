project code:
import { useState } from "react";
import { Card } from "@/components/ui/card";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Check, X, Shield, Eye, EyeOff } from "lucide-react";

interface PasswordRequirement {
  label: string;
  test: (password: string) => boolean;
}

const requirements: PasswordRequirement[] = [
  { label: "At least 8 characters", test: (pwd) => pwd.length >= 8 },
  { label: "Contains uppercase letter", test: (pwd) => /[A-Z]/.test(pwd) },
  { label: "Contains lowercase letter", test: (pwd) => /[a-z]/.test(pwd) },
  { label: "Contains number", test: (pwd) => /[0-9]/.test(pwd) },
  { label: "Contains special character", test: (pwd) => /[^A-Za-z0-9]/.test(pwd) },
];

const calculateStrength = (password: string): { level: number; label: string; color: string } => {
  if (!password) return { level: 0, label: "Enter a password", color: "bg-muted" };
  
  const passedRequirements = requirements.filter((req) => req.test(password)).length;
  
  if (passedRequirements <= 2) {
    return { level: 1, label: "Weak", color: "bg-strength-weak" };
  } else if (passedRequirements <= 4) {
    return { level: 2, label: "Medium", color: "bg-strength-medium" };
  } else {
    return { level: 3, label: "Strong", color: "bg-strength-strong" };
  }
};

const PasswordChecker = () => {
  const [password, setPassword] = useState("");
  const [showPassword, setShowPassword] = useState(false);
  const strength = calculateStrength(password);
  const progress = password ? (strength.level / 3) * 100 : 0;

  return (
    <div className="min-h-screen flex items-center justify-center p-4" style={{ background: "var(--gradient-bg)" }}>
      <Card className="w-full max-w-md p-8 space-y-6 backdrop-blur-sm border-2">
        <div className="text-center space-y-2">
          <div className="flex justify-center mb-4">
            <Shield className="w-16 h-16 text-primary" />
          </div>
          <h1 className="text-3xl font-bold text-foreground">Password Strength Checker</h1>
          <p className="text-muted-foreground">Create a secure password that meets all requirements</p>
        </div>

        <div className="space-y-4">
          <div className="space-y-2">
            <div className="relative">
              <Input
                type={showPassword ? "text" : "password"}
                placeholder="Enter your password"
                value={password}
                onChange={(e) => setPassword(e.target.value)}
                className="h-12 text-lg border-2 focus:ring-2 transition-all pr-12"
              />
              <Button
                type="button"
                variant="ghost"
                size="icon"
                className="absolute right-1 top-1/2 -translate-y-1/2 h-10 w-10 hover:bg-secondary"
                onClick={() => setShowPassword(!showPassword)}
              >
                {showPassword ? (
                  <EyeOff className="h-5 w-5 text-muted-foreground" />
                ) : (
                  <Eye className="h-5 w-5 text-muted-foreground" />
                )}
              </Button>
            </div>
          </div>

          {/* Strength Bar */}
          <div className="space-y-2">
            <div className="flex justify-between items-center">
              <span className="text-sm font-medium text-muted-foreground">Strength:</span>
              <span className={`text-sm font-bold ${
                strength.level === 1 ? "text-strength-weak" :
                strength.level === 2 ? "text-strength-medium" :
                strength.level === 3 ? "text-strength-strong" :
                "text-muted-foreground"
              }`}>
                {strength.label}
              </span>
            </div>
            <div className="h-3 bg-secondary rounded-full overflow-hidden">
              <div
                className={`h-full transition-all duration-500 ease-out ${strength.color}`}
                style={{ width: `${progress}%` }}
              />
            </div>
          </div>

          {/* Requirements List */}
          <div className="space-y-2 pt-2">
            <p className="text-sm font-medium text-muted-foreground mb-3">Password Requirements:</p>
            {requirements.map((req, index) => {
              const passed = req.test(password);
              return (
                <div
                  key={index}
                  className={`flex items-center gap-3 p-3 rounded-lg transition-all duration-300 ${
                    passed ? "bg-strength-strong/10" : "bg-secondary"
                  }`}
                >
                  <div
                    className={`flex-shrink-0 w-5 h-5 rounded-full flex items-center justify-center transition-all duration-300 ${
                      passed ? "bg-strength-strong" : "bg-muted"
                    }`}
                  >
                    {passed ? (
                      <Check className="w-3 h-3 text-white" />
                    ) : (
                      <X className="w-3 h-3 text-muted-foreground" />
                    )}
                  </div>
                  <span
                    className={`text-sm transition-colors duration-300 ${
                      passed ? "text-foreground font-medium" : "text-muted-foreground"
                    }`}
                  >
                    {req.label}
                  </span>
                </div>
              );
            })}
          </div>
        </div>
      </Card>
    </div>
  );
};

export default PasswordChecker;